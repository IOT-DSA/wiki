# Node API

The Node API is a protocol for which all implementing software must conform to. It provides a common
data set to be provided for any software that needs data from multiple devices or software. This
allows devices and software to interoperate with each other based on the data that is provided.

The protocol operates using the JSON format.

## Requests

Requests are used to retrieve data from a provider. A request always contains a method to execute
and a request ID at minimum. The method being invoked may or may not require more fields when
performing a request. Requests must be tracked by their ID until the stream is deemed closed by the
provider. The broker must track IDs for each individual client separately to avoid any ID conflict.

### Fields

Path is a special case field. If the method does not require it then it is deemed as an optional
field.

- rid (integer)
  - The request ID is how the request gets identified. All responses are asynchronous and this field
  is what is used to differentiate the requests.
  - IDs cannot be reused except in the special case to unsubscribe from data points.
- [method](methods) (string)
  - The [method](methods) is used to determine what type of request you are making.
- path (string)
  - The path is where the method should perform the operation on.
  - The path must be valid

#### Example
```javascript
{
  "requests":[
  {
    "rid":2,
    "method":"list",
    "path":"/connections/dslink1"
  }
  ]
}
```

## Responses

Responses are asynchronously returned to the corresponding request. Data is provided through
responses to the original requester. They can be streamed to provided subscription updates or provide
partial responses. Responses can (and often will) provide more fields than those listed below.

### Fields

- rid (integer)
  - The rid is the identifier to the corresponding request.
  - If the request ID is -1, then it is a subscription update.
- stream (enum string, optional)
  - The stream determines whether data will keep flowing or not.
  - If stream is omitted then it is assumed to be same value as previous stream response of same rid, default value is "initialize" if it's not shown in the first response
  - The following values are acceptable, any others will be rejected
      - initialize
          - server hasn’t finished sending existing/cached data, the values sent thus far are incomplete
      - open
          - all existing/cached data are sent, there will still be new update
          - if data provider receive a request but has nothing to send back, it still needs to send back a blank updates, with stream="open", so data consumer knows there are no cached data for the stream.
      - closed
          - the stream will no longer have any update, data consumer can close the stream
- updates (array, optional)
  - Each row can be one of these 2 format
    - a row/list with same number of items as columns structure
    - a map with key:value pairs
      - key can be column name or a rowMeta value
      - rowMeta is always optional
      - when required column is omitted, used the default value defined in column otherwise use null

#### Example of stream that use list for row structure
```javascript
{
  "responses":[
  {
    "rid":2,
    "stream":"open",
    "updates":[
      ["$is","node"],
      ["$permission":"write"],
      ["@city","San Francisco"],
      ["point1",{"$is":"temperaturePoint", "@name":"Custom Name for Point1"}],
      ["point2",{"$is":"numericPoint"}]
    ]
  }
  ]
}
```
#### Example of stream that use map for row structure
```javascript
{
  "responses":[
  {
    "rid":2,
    "stream":"open",
    "updates":[
      {"name":"point2","change":"removed"}
    ]
  }
  ]
}
```

## Error
If any error happened, stream response will have a error object showing the information about the error
```javascript
{
  "responses":[
  {
    "rid": 1,
    "stream":"closed",
    "error": {
      "type": "PermissionDenied",
      "phase": "request",
      "path":"/connection/dslink1"
      "meg": "permission denied",
      "detail": "user Steve is not allowed to access data in '/connection/dslink1'"
    }
  }
  ]
}
```


 - **error** error object
   - **msg**  required, a short description of the error
   - **type**  optional, a standard error code if the error type is known
     - predefined error structure are in /defs/error
   - **phase**  optional, indicate whether the error happen on request or response, if omitted it means "request" phase
   - **path**  optional, on which path this error happened, can be omitted if it's same path as the path in request.
   - **detail**  a detail message of the error, can be the stack trace or other message
     - when /settings/$errorDetail = false, error won’t contain detail message
   - anything else to describe the error
