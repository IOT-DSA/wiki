dslink.json is the basic config and description file for every dslink application.
it's a json document that shares most of the properties same as package.json: https://docs.npmjs.com/files/package.json

## custom properties
### configs
config file that's loaded by the dslink app, also managed by the dslink manager
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
 * used by dslink manager to check if config is valid

### getDependencies
```
  "getDependencies": [
    "pub get"
  ]
```