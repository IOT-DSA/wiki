# Methods

Methods are used in a request to retrieve data. They specify what type of data is needed and will
return it. Permissions affect how data is interacted with by a user.

Request fields are sent in the top level JSON request. Response fields return in the update field,
methods will go into more detail on how updates are received. All responses are a map in the first
element of the update field unless otherwise specified by the method.

#### List of methods

- list
- set
- remove
- invoke
- subscribe
- unsubscribe

## List

This method will retrieve a list of children. However, children without the read permission will
not be displayed.

The response of list will update on any node change available to the current requester, there is no need to list on same node twice in same requester because second stream will return same state as the previous list stream's final state.

#### Request fields

 - path (type:path)
   -  path of the node to run the list api

#### Response columns
 - name
  - name of the field
  - names start with $ are [configs](Configs) that can affect how system works
    - "$is"
      - profile of the node, must be the first update of a list stream, otherwise requester will treat it as a new node and clear other existing data
      - profile node should also be loaded to get all predefined configs/attributes of a node
      - profile nodes locate in /defs/profile/
    - "$mixin"
      - attributes and configs mixin of a node, multiple mixins are separated with "|"
      - when mixin path starts with "/", it's a absolute node path
      - when mixin path starts doesn't start with "/", it's a node path relative to the current node being listed
    - "$permission"
      - a enum of the following values
        - **read** user has read access to the node, can be omitted
        - **write** user has write access to the node, can read node and write to node and its attributes
        - **config** user has full access to the node, can read/write and change configs 
   - names start with @ are custom attributes
   - other names are children nodes
   - name should not be blank or contain these characters: . / \ ? % * : | “ < >  this is basicly rules for file names, plus “.” (dot) is not allowed either

 - value
  - updated value of the field

#### Response meta
 - change (optional enum)
   - enum value, can be "remove" or "update"
   - default value is "update"

#### Example usage

##### Request
```json
{
  "rid": 1,
  "method": "list",
  "path": "/light"
}
```

##### Response
```json
{
  "rid": 1,
  "stream": "open",
  "update": [
    [
      "$is",
      "point/light"
    ],
    [
      "$mixin",
      "myMixin"
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
```

## Set

This method will set values on a node.

#### Request fields
 - path (type:path)
   -  path of the node to set, can be a node path or attribute/config path
 - value (any primitive type)
  - The type of the value must be acceptable to the type of the node.

#### Response columns

No response update.

#### Example usage

The value in this usage turns the lights on (true) or off (false).

##### Request
```json
{
  "rid": 1,
  "method": "set",
  "path": "/lights/Lights A",
  "value": false
}
```

##### Response
```json
{
  "rid": 1,
  "stream": "closed"
}
```

## Remove

This method will remove attributes or configs.
Remove a overwritten config/attribute value will restore it to default value defined in profile or mixin

#### Request fields

 - path (type:path)
   -  attribute/config path to remove

#### Response columns

No response update.

#### Example usage

##### Request
```json
{
  "rid": 1,
  "method": "remove",
  "path": "/light/@city"
}
```

##### Response
```json
{
  "rid": 1,
  "stream": "closed"
}
```

## Invoke

This method will invoke an action on a node.
params list and

#### Request fields

- params (map, optional)
  - The parameters are defined in the action node
  - Can be omitted if the action takes no parameters

#### Response columns

 columns list is defined in the action node.

#### Example usage


##### Request
```json
{
  "rid": 1,
  "method": "invoke",
  "path": "/lights/testAction",
  "params": {
    "username": "dsaisawesome"
  }
}
```

##### Response
```json
{
  "rid": 1,
  "stream": "closed",
  "updates": [
    {
      "result": "yeah"
    }
  ]
}
```

## Subscribe

This method will subscribe to a datapoint node to receive its value and value updatea.


#### Request fields

 - paths (type:list)
   -  a list of node path to subscribe

#### Response columns

No response update to the direct response.
the value update will be sent in the main subscription stream, which has the following columns

There are multiple elements in the updates field. Each element will contain:

- path 
  - path of the node
- value
  - updated value
- ts (time string in iso8601 format)
  - last update time of the value

#### Response meta
 - status (option string)
   - status of the value
 - count (optional int)
   - if the response skip some value, this shows how many updates have happened since last response 
   - (count-1) of the previous updates are skipped. so count is always greater than 1 when specified
 - sum (optional number)
   - the sum value if one or more numeric value is skipped
 - max (optional number)
   - the max value if one or more numeric value is skipped
 - min (optional number)
   - the min value if one or more numeric value is skipped
#### Example usage

##### Request
```json
{
  "rid": 1,
  "method": "subscribe",
  "paths": ["/lights/Lights A"]
}
```

##### Response
```json
{
  "rid": 1,
  "stream": "closed"
}
```

##### Update
"rid":0 is reserved for the subscription update and this stream will always be open

```json
{
  "rid": 0,
  "updates": [
    [
      "/node1/point1",
      12,
      "2014-11-27T09:11.000-08:00"
    ],
    {
      "path": "/node1/point2",
      "status": "disconnected"
    },
    {
      "path": "/node1/point3",
      "value": 10,
      "ts": "2014-11-27T09:11.000-08:00",
      "count": 5,
      "sum": 75,
      "min": 10,
      "max": 20
    }
  ]
}
```

## Unsubscribe

This method will unsubscribe from a datapoint and stop receiving new values on a node. 

#### Request fields

 - paths (type:list)
   -  a list of node path to subscribe

#### Response columns

No response fields sent.


#### Example usage

##### Request
```json
{
  "rid": 1,
  "method": "unsubscribe",
  "paths": ["/lights/Lights A"]
}
```

##### Response
```json
{
  "rid": 1,
  "stream": "closed"
}
```


## Close

This method will close a stream

#### Example usage

##### Request
```json
{
  "rid": 1,
  "method": "close"
}
```

##### Response
The response of the close method is optional.
```json
{
  "rid": 1,
  "stream": "closed"
}
```