The dslink.json file is the basic configuration and description for every DSLink application.
It's a JSON document that shares most of the properties same as package.json from [npm](https://docs.npmjs.com/files/package.json).

## Properties
### name
Name of the DSLink.

### version
Version of the DSLink.

### description
Description of the DSLink.

### license
License of the DSLink.

### author
JSON object containing information about the DSLink author.
#### name
Name of the author.
#### email
Email of the author.

### main
Command that is ran to start the DSLink, it may contain parameters.

### repository
Contains data about the DSLink source code control repository.
#### type
Source code control type, examples include git, svn, and hg.
#### url
URL to repository viewer.

### bugs
#### url
URL to bugtracker for DSLink.

### configs
Provides configuration parameters that are passed to the DSLink. These are also managed by the DSLink manager in DGLux Server.
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
List of commands that are ran to set the DSLink's environment up.
```
  "getDependencies": [
    "pub get"
  ]
```

## Example
```json
{
  "name": "dslink-java-dsa-coap",
  "version": "0.0.2",
  "description": "DSA-over-COAP",
  "license": "Apache",
  "author": {
    "name": "Kenneth Endfinger",
    "email": "kaendfinger@gmail.com"
  },
  "main": "bin/dslink-java-dsa-coap",
  "repository": {
    "type": "git",
    "url": "https://github.com/IOT-DSA/dslink-java-dsa-coap"
  },
  "bugs": {
    "url": "https://github.com/IOT-DSA/dslink-java-dsa-coap/issues"
  },
  "configs": {
    "name": {
      "type": "string",
      "default": "DSA-COAP"
    },
    "broker": {
      "type": "url"
    },
    "token": {
      "type": "string"
    },
    "nodes": {
      "type": "path",
      "default": "nodes.json"
    },
    "key": {
      "type": "path",
      "default": ".key"
    },
    "log": {
      "type": "enum",
      "default": "info"
    },
    "handler_class": {
      "type": "string",
      "default": "org.dsa.iot.coap.CoapHandler"
    }
  }
}
```