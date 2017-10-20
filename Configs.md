## Config Naming
 - Config names that start with **$** are readable to all requesters.
 - Config names that start with **$$** are only readable to requesters with config permission.
 - Config names that start with **$$$** always returns a null value in the list method.

## Core Configs
 - **$base**
   - the base path used by profile, and interface
   - $base must be the first update in a list api, otherwise it's null
 - **$is** (require:read)
   - defines the profile of a node
   - profile can define configs, attributes and children nodes
   - profile paths are paths relative to the $base/defs/profile/
   - $is must be the second update in a list api, or the first when $base is null
 - **$disconnectedTs**
   - timestamp of when the remote node get disconnected
   - this value should be null or undefined when device is connected
 - **$name** (require:read)
   - custom display name for a node
   - change $name won't affect node's path or its name in parent node
 - **$type** (require:read)
   - type of subscription value
   - node is not subscribable if $type is not specified
 - **$writable** (require:read)
   - whether the set method can be called to this node
   - only valid on a node with value type ($type)
   - value can be "write" "config"
   - "never" is the default value when not specified
 
 - **$params** (require:read)
   - parameters for a action node, required for any action node (or its profile)
     - action with no parameters data still need a blank $params config
   - option for each parameter item
     - name, name of the parameter
     - type, type of the parameter, defined in /defs/type/
     - default, default value of the parameter
     - description, description of the parameter, will be shown in element tooltip (title)
     - placeholder, placeholder when the editor is text input
     - other option defined by the type
```javascript
  "$params":[
    {"name":"count","type":"int","min":1,"default":1},
    {"name":"timerange","type":"timerange"}
  ]
```
 - **$columns** (require:read)
   - column structure for a action node's response stream, required for any action node (or its profile)
     - action with no response data still need a blank $columns config
   - option for each column item
     - name, name of the column 
     - type, type of the column , defined in /defs/type/
     - default, default value of the column
     - other option defined by the type
 - **$result** (require:read, type:enum[values,table,stream]))
   - result type when node is invoked as an action
     - values: the default result type, usually has only one row of values
     - table: all rows together in the updates represent the action result as a table
     - stream: every row in the updates is an update of the result, the response stream won't be closed from server and will keep getting updates from server

```javascript
  "$columns":[
    {"name":"ts","type":"time"},
    {"name":"value","type":"object"}
  ]
```
 - **$streamMeta** (require:read)
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
 - **$invokable** (require:read)
    - permission needed to invoke a action node
    - value can be "read" "write" "config" or "never"
    - "never" is the default value if not specified

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
