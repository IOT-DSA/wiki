dslink.json is the basic config and description file for every dslink application.
It's a JSON document that shares most of the properties same as package.json from [npm](https://docs.npmjs.com/files/package.json).

## Custom Properties
### configs
config file that's loaded by the DSLink app, also managed by the DSLink manager.
```
  "configs": {
    "broker": {
      "type": "url"
    },
    "nodes": {
      "type": "path",
      "value": "nodes.json",
      "required": true
    },
    "key": {
      "type": "path",
      "value": ".dslink.key",
      "required": true
    }
  }
```
* type
 * string
 * number
 * int
 * bool
 * url
 * path (file or uri path)
* required (bool)
 * Used by dslink manager to check if config is valid

### getDependencies
```
  "getDependencies": [
    "pub get"
  ]
```