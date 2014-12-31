# Configs

# Core Configs
 - **$is** (require:read, editable:never)
   - defines the profile of a node
   - profiles start with "/" are absolute path
   - other profile paths are path relative to the /defs/profile/
 - **$mixin** (require:read, editable:config)
   - attributes mixins of a node, multiple profile are separated by "|"
   - mixins start with "/" are absolute path
   - other mixin paths are path relative to the current node
 - **$permission** (require:read, editable:never, type:enum[read,write,config])
   - not store in node data, generated for list response based on the permission of current requester
 - **$permissions** (require:config, editable:config)
   - a list of permission rules defines permission for a user or a group
```javascript
	"$permissions":[
		{"group":"DGLogik", "allow":"config"},
		{"user":"Samrg", "allow":"config"},
		{"group":"QA", "allow":"read"},
		{"group":"guest", "allow":"none"}
	]
```
 - **$type** (require:read, editable:never)
   - type of subscription value
   - node is not subscribable if $type is not specified
 - **$settings** (require:read, editable:never)
   - only used by profile nodes, defines a map of configs for node setting
   - configs defined in settings must be specified by nodes that implement this profile
   - options for each setting item
     - type, type of the setting config, defined in /defs/type/
     - require, permission required to read this config, can be "read","write","config"
     - editable, permission required to write this config, can be "read","write","config","never"
     - other option defined by the type
   - if any config not defined in settings map but shows up in node or node profile, it use this default setting
     - {"require":"read", "editable":"never"}
```javascript
	"$settings":{
		"$max":{"type":"number", "require":"read", "editable":"config"},
		"$min":{"type":"number", "min":-273.15, "require":"read", "editable":"config"},
		"$precision":{"type":"int", "min":0, "require":"read", "editable":"never"},
		"$status":{"type":"enum/pointStatus", "require":"read", "editable":"never"},
		"$unit":{"type":"string", "require":"read", "editable":"never"}
	}
```
 - **$params** (require:read, editable:never)
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
 - **$columns** (require:read, editable:never)
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
 - **$streamMeta** (require:read, editable:never)
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