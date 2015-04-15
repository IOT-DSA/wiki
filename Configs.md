## Core Configs
 - **$is** (require:read, writable:never)
   - defines the profile of a node
   - profile can define configs, attributes and children nodes
   - profile paths are patsh relative to the $basePath/defs/profile/
 - **$interface** (require:read, writable:never)
   - defines the interfaces of a node or a profile, multiple interfaces are separated by "|"
   - interface can define configs, attributes and children nodes
   - interface paths are path relative to the $basePath/defs/profile/
   - interfaces defined for a profile must be fully implemented in the profile
   - interfaces defined for a node must be implemented by either the node or the profile
 - **$mixin** (require:read, writable:config)
   - attributes/configs mixins of a node, multiple mixins are separated by "|"
   - attributes can define configs, attributes but NOT children nodes
   - mixin can not modify readonly configs
   - mixin paths are paths relative to the $basePath
   - when merging properties from a mixin node, profile and mixins inside the mixin are ignored
 - **$basePath**
   - the base path used by mixin, profile, and interface
 - **$disconnectedTs**
   - timestamp of when the remote node get disconnected
   - this value should be null or undefined when device is connected
 - **$permission** (require:read, writable:never, type:enum[read,write,config])
   - not store in node data, generated for list response based on the permission of current requester
 - **$$permissions** (require:config, writable:config)
   - a list of permission rules defines permission for a group or a requester
```javascript
  "$$permissions":[
    {"group":"DGLogik", "allow":"config"},
    {"id":"Samrg", "allow":"config"},
    {"group":"QA", "allow":"read"},
    {"group":"guest", "allow":"none"}
  ]
```
 - **$name** (require:read, writable:config)
   - custom display name for a node
   - change $name won't affect node's path or its name in parent node
 - **$type** (require:read, writable:never)
   - type of subscription value
   - node is not subscribable if $type is not specified
 - **$writable** (require:read, writable:never)
   - whether the set method can be called to this node
   - only valid on a node with value type ($type)
   - value can be "write" "config" or "never"
   - "never" is the default value if not specified
 - **$settings** (require:read, writable:never)
   - only used by profile nodes, defines a map of configs for node setting
   - configs defined in settings must be specified by nodes that implement this profile
   - options for each setting item
     - type, type of the setting config, defined in /defs/type/
     - require, permission required to read this config, can be "read","write","config"
     - writable, permission required to write this config, can be "read","write","config","never"
     - other option defined by the type
   - if any config not defined in settings map but shows up in node or node profile, it use this default setting
     - {"require":"read", "writable":"never"}
```javascript
  "$settings":{
    "$max":{"type":"number", "require":"read", "writable":"config"},
    "$min":{"type":"number", "min":-273.15, "require":"read", "writable":"config"},
    "$precision":{"type":"int", "min":0, "require":"read", "writable":"never"},
    "$status":{"type":"enum/pointStatus", "require":"read", "writable":"never"},
    "$unit":{"type":"string", "require":"read", "writable":"never"}
  }
```
 - **$params** (require:read, writable:never)
   - parameters for a action node, required for any action node (or its profile)
     - action with no parameters data still need a blank $params config
   - option for each parameter item
     - name, name of the parameter
     - type, type of the parameter, defined in /defs/type/
     - default, default value of the parameter
     - other option defined by the type
```javascript
  "$params":[
    {"name":"count","type":"int","min":1,"default":1},
    {"name":"timerange","type":"timerange"}
  ]
```
 - **$columns** (require:read, writable:never)
   - column structure for a action node's response stream, required for any action node (or its profile)
     - action with no response data still need a blank $columns config
   - option for each column item
     - name, name of the column 
     - type, type of the column , defined in /defs/type/
     - default, default value of the column
     - other option defined by the type
```javascript
  "$columns":[
    {"name":"ts","type":"time"},
    {"name":"value","type":"object"}
  ]
```
 - **$streamMeta** (require:read, writable:never)
   - stream meta data of a action node, optional
   - when stream meta is needed by a row in the stream, this row has to use the map format instead of list format
   - option for each streamMeta item
     - name, name of the streamMeta 
     - type, type of the streamMeta , defined in /defs/type/
     - default, default value of the streamMeta 
     - other option defined by the type
```javascript
  "$streamMeta":[
    {"name":"status","type":"string"}
  ]
```
 - **$invokable** (require:read, writable:never)
    - permission needed to invoke a action node
    - value can be "read" "write" "config" or "never"
    - "never" is the default value if not specified

## Config Overwrite
 - configs directly defined in profile can not be overwritten.
 - configs defined in profile $settings must be implemented by mixin or by the node itself
   - if both mixin and node defined same config, the config value in node overwrite the mixin
   - if multiple mixins contain duplicated configs, the latter one overwrite the previous mixin
   - these rules applied to attribute overwrite too

## Permission Values
low to high
 - **none**
   - lowest permission, means requester is not allowed to do anything.
   - used by config: $$permissions
 - **read**
   - requester can read a node with list request
     - all attributes are shown
     - configs which require:read are shown
     - children nodes that have read permission are shown
   - requester can subscribe to node value if $type is defined for this node
   - used by configs: $$permissions, $permission, $require, $settings, $require
 - **write**
   - requester can send set request to change node attributes
   - requester can change configs or run actions that require write permission
   - used by configs: $$permissions, $permission, $require, $settings, $writable
 - **config**
   - requester can change configs or run actions that require config permission
   - used by configs: $$permissions, $permission, $require, $settings, $writable
 - **never**
   - No user can have the 'never' permission as this is meant to prevent system admins from overwriting
a configuration that only the system manages.
   - highest permission that will never happen on any requester
   - used by configs: $settings, $writable