dslink.json is the basic config and description file for every dslink application.
it's a json document that shares most of the properties same as package.json: https://docs.npmjs.com/files/package.json

## custom properties
### configs
config file that's loaded by the dslink app, also managed by the dslink manager
```
  "configs": {
    "broker": {
      "type": "url",
      "required": false
    },
    "nodes": {
      "type": "path",
      "default": "ds_nodes.json",
      "required": true
    },
    "key": {
      "type": "path",
      "default": ".dslink.key",
      "required": true
    }
  }
```

### getDependencies
```
  "getDependencies": [
    "pub get"
  ]
```