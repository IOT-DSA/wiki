Within `server.json`, the `defaultPermission` config is a permission list that defines the capabilities of each permission group in relation to the root node.

* The first value of each item in `defaultPermission` is the [permission group] (https://github.com/IOT-DSA/docs/wiki/DSA-Permission-Model). This can be a username, dsId, custom user group, or "default."
* The second value of each item is the [permission value] (https://github.com/IOT-DSA/docs/wiki/DSA-Permission-Model). This can be `config`, `write`, `read`, `list`, or `none`.

#### example 
```
   "defaultPermission": [
      [":config","config"],
      [":write","write"],
      [":read","read"],
      [":user","read"],
      ["default","read"]
   ]
```