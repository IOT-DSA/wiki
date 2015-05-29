# Methods

Methods are used in a request to retrieve data. They specify what type of data is needed and will
return it. Permissions affect how data is interacted with by a user.

Request fields are sent in the top level JSON request. Response fields return in the updates field,
methods will go into more detail on how updates are received.

#### List of methods

- list
- set
- remove
- invoke
- subscribe
- unsubscribe
- close

## List

This method will retrieve a list of children, attributes, and configs. However, children without the read permission will
not be displayed.

The response of list will update on any node change available to the current requester, there is no need to list on same node twice in same requester because second stream will return same state as the previous list stream's final state.

#### Request fields

- path (string)
  - Path of the node to retrieve a list on

#### Response columns
 - name
  - Name of the field
  - Names that start with '$' are [configs](Configs) that can affect how system works
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
   - Names that start with '@' are custom attributes
   - Other names are children nodes, with map value of the children properties
        - children node must include **$name** if it has a display name
        - children node must include **$is** in the child object
        - children node must include **$mixin** if it's not empty
        - children node must include **$permission** if it's not 'read'
        - children node should include **$invokable** if user is allowed to invoke it 
        - children node should include **$type** if node support subscription
        - children node should include **$interface** if it's defined
          - **$interface** are not needed if it's already defined in $is profile.
        - other properties of children nodes shouldn't appear in the list response.
   - Names must not be blank or contain these characters: ```. / \ ? * : | “ < >```
   - When % is used in name, it must be used only for UTF-8 based percent encode, not for other purpose. 

 - value
  - updated value of the field

#### Response meta
 - change (optional enum configuration)
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
        "$name": "Custom Name for Point1",
        "$invokable": "read",
        "$interface": "tempUnit"
      }
    ],
    [
      "point2",
      {
        "$is": "numericPoint",
        "$invokable": "write",
      }
    ],
    {
      "name": "point3",
      "change": "remove"
    }
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

 columns list is defined in the action node or its profile
 but invoke response can also return a column structure to overwrite the value
 the columns structure can only be returned once per action invoke

#### Response updates (rows)
 
 columns results can have one or multiple rows
 if the invoke response' streamstatus is still "open", it can have more updates rows in future response until requester or responder close the stream

#### Example usage

##### Request
```json
{
  "rid": 1,
  "method": "invoke",
  "path": "/echo",
  "params": {
    "string": "dsaisawesome"
  }
}
```

##### Response
```json
{
  "rid": 1,
  "stream": "closed",
  "columns": [
    {"name": "result", "type": "string"}
  ],
  "updates": [
    ["dsaisawesome"]
  ]
}
```

##### Stream Response
```json
{
  "rid": 1,
  "stream": "open",
  "columns": [
    {"name": "changes", "type": "string"}
  ],
  "updates": [
    ["first update"]
  ]
}
```

```json
{
  "rid": 1,
  "stream": "closed",
  "updates": [
    ["second update"]
  ]
}
```

## Subscribe

This method will subscribe to a datapoint node to receive its value and value updatea.

#### Request fields

 - paths (type:list)
   - a list of JSON objects about the subscription
     - path - Path of the node
     - sid - Subscription ID
     - cache (optional, default of 1) - The cache of values the responder contains. If the responder can't write a subscription update to the requester then it will use the cache of values.

#### Response columns

No response update to the direct response.
the value update will be sent in the main subscription stream, which has the following columns

There are multiple elements in the updates field. Each element will contain:

- sid
  - Subscription ID (requester must keep track of which SIDs belong to which node)
- value
  - updated value
- ts (time string in iso8601 format)
  - last update time of the value

#### Response meta
 - status (optional enum)
   - status of the value
     - ok - No issues with the node value (default is status is omitted)
     - stale - The value could potentially be out of date
     - disconnected - Never set by a dslink. The broker sets this if a connection is lost between the dslink and the broker.
 - count (optional int)
   - if the response skip some value, this shows how many updates have happened since last response 
   - only valid value is counted, if value status is not ok, and count is not specified explicitly, the count this updated is treated as 0\
   - skip value will happen when responder connection is slow, or when change happens on requester fater than the updateInterval
 - sum (optional number)
   - the sum value if one or more numeric value is skipped
   - only valid numeric value is added to sum
 - max (optional number)
   - the max value if one or more numeric value is skipped
 - min (optional number)
   - the min value if one or more numeric value is skipped
 - part (optional string)
   - used when sending a very big string or very big List value
   - the value of part will be a fraction number, i.e. "1/2" "2/2" 
   
#### Example usage

##### Request
```json
{
  "rid": 1,
  "method": "subscribe",
  "paths": [
    {"path": "/point1", "sid": 0, "cache": 5},
    {"path": "/point2", "sid": 1},
    {"path": "/point3", "sid": 2} 
  ]
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
      0,
      12,
      "2014-11-27T09:11.000-08:00"
    ],
    {
      "sid": 1,
      "status": "disconnected",
      "ts": "2014-11-27T09:11.000-08:00"
    },
    {
      "sid": 2,
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

 - sids (list)
   -  a list of subscription IDs to unsubscribe

#### Response columns

No response fields sent.


#### Example usage

##### Request
```json
{
  "rid": 1,
  "method": "unsubscribe",
  "sids": [0]
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
If request is closed from requester side, response is not needed