## Overview

NodeAPI is a stateful and lightweight streaming remote procedure call (RPC) protocol. Primarily, this specification defines several data structures and the rules around their processing. The concepts can be used over various transport layers, such as the same process, HTTP, web sockets, sockets, and various other message-passing environments. By default, NodeAPI uses [JSON](http://www.json.org/) ([RFC 4627](http://www.ietf.org/rfc/rfc4627.txt)) as the data format. However the protocol is serialization-format-independent, and can be transported using formats like MsgPack.

## Conventions

The Requester originates Request objects and handles Response objects. The Responder originates Response objects and handles Request objects. 

A single implementation of this specification can perform both roles, even at the same time, to other different requesters or the same requester. This specification does not address that complexity.

## Requests

Requests are used to retrieve data from a provider. A request always contains, at a minimum, a method to be executed and a request ID. The method being invoked might require more fields. Requests must be tracked by their ID until the stream is deemed closed by the
responder. To avoid conflicts, the responder must track IDs for each individual requester separately.

### Fields

Path is a special case: if the method does not require it then it is deemed as an optional
field.

- rid (integer)
  - The request ID, which is how the request gets identified. All responses are asynchronous and this field
  is used to differentiate the requests.
  - rid cannot be reused by another request except when closing the stream.
  - Request rid must be greater than 0.
  - The max value of an rid is 2147483647(0x7FFFFFFF), after that, the next rid must be reset to 1.
- [method](methods) (string)
  - The [method](methods) is used to determine what type of request you are making.
- path (string)
  - The path specifies where the method is to perform the operation. The path must be valid.

#### Example
```json
{
  "msg": 15,
  "requests":[
    {
      "rid": 2,
      "method": "list",
      "path": "/connections/dslink1"
    }
  ]
}
```

## Responses

Responses are returned to the requestor asynchronously. Data is provided through
responses to the original requester. They can be streamed to provided subscription updates or provide
partial responses. Responses can contain more fields than those listed below.

### Fields

- rid (integer)
  - The identifier of the request to which the response is addressed.
  - rid:0 is reserved for the subscription update
- stream (enum string, optional)
  - The stream determines whether data will keep flowing.
  - If stream is omitted, it is assumed to be same value as previous stream response of same rid, default value is "initialize" if it is not shown in the first response
  - Valid values:
      - initialize
          - responder hasn’t finished sending existing or cached data
      - open
          - there is no data cached for the stream at the moment, but subsequent updates might be sent
      - closed
          - all data has been sent, data consumer can close the stream
- updates (array, optional)
  - Array rows are sent in one of the following formats:
    - a row/list with same number of items as columns structure
    - a map with key:value pairs
      - key can be column name or a meta value
      - when required column is omitted, use the default value if defined for the column, otherwise use null
 - columns (array, optional)
   - column structure of the response, for response to an "invoke" method
   - when an action node or its profile already defined the column structure, the this field is not needed
   - column structure only needs to be returned once unless it changes at run time

#### Example of stream that use list for row structure
```json
{
  "msg": 16,
  "responses": [
    {
      "rid": 2,
      "stream": "open",
      "updates": [
        [
          "$is",
          "node"
        ],
        [
          "$permission",
          "write"
        ],
        [
          "@city",
          "San Francisco"
        ],
        [
          "point1",
          {
            "$is": "temperaturePoint",
            "@name": "Custom Name for Point1"
          }
        ],
        [
          "point2",
          {
            "$is": "numericPoint"
          }
        ]
      ]
    }
  ]
}
```

#### Example of stream that use map for row structure
```json
{
  "msg": 17,
  "responses": [
    {
      "rid": 2,
      "stream": "open",
      "updates": [
        {
          "name": "point2",
          "change": "removed"
        }
      ]
    }
  ]
}
```

#### Example with custom columns in the response
```json
{
  "msg": 18,
  "responses": [
    {
      "rid": 2,
      "stream": "open",
      "columns": [
        {
          "name": "ts",
          "type": "time"
        },
        {
          "name": "value",
          "type": "number"
        }
      ],
      "updates": [
        [
          "2014-01-01T11:21:23.00-08:00",
          45
        ]
      ]
    }
  ]
}
```

## Error
If any error happened, stream response will have a error object showing the information about the error
```json
{
  "msg": 19,
  "ack" : 7,
  "responses": [
    {
      "rid": 1,
      "stream": "closed",
      "error": {
        "type": "permissionDenied",
        "phase": "request",
        "path": "/connection/dslink1",
        "msg": "permission denied",
        "detail": "user Steve is not allowed to access data in '/connection/dslink1'"
      }
    }
  ]
}
```


 - **error** error object
   - **msg**  optional, a short description of the error
   - **type**  optional, a standard error code if the error type is known
     - predefined error structure are in /defs/error
     - a error must have either a type or a msg or both, when msg is omitted, type is the camelCase formatting of msg
   - **phase**  optional, indicate whether the error happen on request or response, if omitted it means "request" phase
   - **path**  optional, on which path this error happened, can be omitted if it's same path as the path in request.
   - **detail**  a detail message of the error, can be the stack trace or another message
     - when /settings/$errorDetail = false, error won’t contain detail message
   - anything else to describe the error

## msg and ack
Every message has an ID. When a DSLink or broker receives a response or request, it must return and ACK with the same ID. The ACK can be a standalone message or merged into normal message.

The maximum value of a message ID is 2147483647 (0x7FFFFFFF), after which IDs must be reset to start at 1.

#### Example:
A -> B
```json
{
  "msg": 10,
  "requests": [
  ]
}
```
B -> A
```json
{
  "msg": 1,
  "ack": 10,
  "responses": [
  ]
}
```
A -> B
```json
{
  "msg": 11,
  "ack": 1
}
```

There is no need to return an ACK if the incoming message doesn't contain any requests or responses.

ACK can be merged: if two or more incoming messages are received before the ACK is sent back, the ACK can be sent with the last msg ID.

#### Behavior of responder when ACK is missing
Responders must provide a way to configure the maximum number of missing ACKs permitted. The default is 8.

 * When the number of missing ACKs exceeds the maximum, a subscription responder must stop sending data and roll up all the pending changes. When a new ACK is received, it should resume sending data.
 * When the number of missing ACKs exceeds the maximum, a list responder must stop sending data and merge all the changes.
 * For streaming invoke, the SDK must provide a way for the callback to know how many ACKs have been missed.