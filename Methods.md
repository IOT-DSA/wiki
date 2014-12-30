# Methods

Methods are used in a request to retrieve data. They specify what type of data is needed and will
return it. Permissions affect how data is interacted with by a user.

Request fields are sent in the top level JSON request. Response fields return in the update field,
methods will go into more detail on how updates are received. All responses are a map in the first
element of the update field unless otherwise specified by the method.

### List of methods

- list
- set
- remove
- invoke
- subscribe
- unsubscribe

## List

This method will retrieve a list of children. However, children without the read permission will
not be displayed.

### Request fields

No extra fields are required.

### Response fields

All attributes of the node are also provided in the response.

- permission (array of strings, configuration)
- mixin (array of strings, configuration)
  - Mixins can provide additional attributes to a node.
- children (map)
  - Children without read permission are not shown.
  - Map fields
    - name (string, attribute)
      - Name of the node.
    - permission (array of strings, configuration)
      - Always has read permission at minimum.
      - If omitted it is assumed to only have read permission.

### Required permission

- Read

### Example usage

#### Request
```javascript
{
  "rid": 1,
  "method": "list",
  "path": "/lights"
}
```

#### Response
```javascript
{
  "rid":1,
  "stream": "initialize",
  "update":[
    ["$is","node"],
    ["$permission":"write"],
    ["@city","San Francisco"],
    ["point1",{"$is":"temperaturePoint", "@name":"Custom Name for Point1"}],
    ["point2",{"$is":"numericPoint"}]
  ]
}
```

## Set

This method will set values on a node.

### Request fields

- value (any type)
  - The type of the value must be acceptable to the type of the node.

### Response fields

No response fields sent.

### Required permission

- Write

### Example usage

The value in this usage turns the lights on (true) or off (false).

#### Request
```javascript
{
  "rid": 0,
  "method": "set",
  "path": "/lights/Lights A",
  "value": false
}
```

#### Response
```javascript
{
  "rid": 0,
  "stream": "closed"
}
```

## Remove

This method will remove attributes or mixins.

### Request fields

No extra fields are required.

### Response fields

No response fields sent.

### Required permission

- Write

### Example usage

#### Request
```javascript
{
  "rid": 0,
  "method": "remove",
  "path": "/def/mixin/location"
}
```

#### Response
```javascript
{
  "rid": 0,
  "stream": "closed"
}
```

## Invoke

This method will invoke an action on a node.

### Request fields

- action (string)
  - The action to invoke on the node.
- params (map, optional)
  - The parameters in the map are to be specified by the action of the node.
  - Can be omitted if the action takes no parameters

### Response fields

- results (map, optional)
  - The results in the map are to be specified by the action of the node.
  - Can be omitted if the action has no results.

### Required permission

- Invoke

### Example usage

In this example we use a Philips Hue lighting bridge with a link action to connect the dslink to
the bridge so it can be controlled.

#### Request
```javascript
{
  "rid": 0,
  "method": "invoke",
  "path": "/lights",
  "action": "link"
  "params": {
    "username": "dsaisawesome"
  }
}
```

#### Response
```javascript
{
  "rid": 0,
  "stream": "closed",
  "results": {}
}
```

## Subscribe

This method will subscribe to a datapoint to receive a new value whenever the node updates its
value. Upon initial subscription, a value will be returned in the response. The stream will always
remain open until the request id is unsubscribed.

### Request fields

No extra fields are required.

### Response fields

There are multiple elements in the updates field. Each element will contain:

- name (string, attribute, optional)
  - If name is omitted, then it is an update to the node value.
  - If name starts with "@" it is an attribute
  - If name starts with "$" it is a configuration
- value (any type)

### Required permission

- Read

### Example usage

#### Request
```javascript
{
  "rid": 0,
  "method": "subscribe",
  "path": "/lights/Lights A"
}
```

#### Response
```javascript
{
  "rid": 0,
  "stream": "open",
  "updates": [
    {
      "value": false
    },
    {
      "@name": "@brightness",
      "value": 50
    }
  ]
}
```

## Unsubscribe

This method will unsubscribe from a datapoint and stop receiving new values on a node. The stream
on the request ID the subscription is holding will be closed. The rid must be the same ID used when
subscribing.

### Request fields

No extra fields are required. Path must be omitted, it is unused in this method.

### Response fields

No response fields sent.

### Required Permission

- None

### Example usage

If the subscription rid is 0, then the request must also be 0 to unsubscribe properly.

#### Request
```javascript
{
  "rid": 0,
  "method": "unsubscribe",
}
```

#### Response
```javascript
{
  "rid": 0,
  "stream": "closed"
}
```
