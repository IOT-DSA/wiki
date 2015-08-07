The DGLux Server has many options that allow you to tweak how the server works and customize it for your own use.

Options are defined in the server.json

An example server.json is:

```json
{
  "debug": false,
  "port": 8080,
  "httpsPort": 8443,
  "certName": "",
  "certPassword": "",
  "disableFileSecurity": false,
  "broadcast": false,
  "workers": 2,
  "updateInterval": 200,
  "static": {},
  "linkConfig": {},
  "disabledLinks": [],
  "uptimeCheckUrl": null,
  "upstream": {},
  "quarantine": false,
  "defaultPermission": null,
  "useRuntimeManager": false,
  "passwordHasherIterations": 1000,
  "passwordHasherKeyLength": 32,
  "brokerName": "broker-",
  "runBrokerInMain": false
}
```

## debug

Enable/Disable Debugging Mode

**Default Value**: false

## port

HTTP Port to listen on. If this is less than or equal to 0, then the server does not listen on any port for HTTP.

**Default Value**: 8080

## httpsPort

HTTPs Port to listen on. If this is less than or equal to 0, and/or `certName` or `certPassword` is empty or not provided, then the server does not listen on any port for HTTPS. Ensure that you setup your SSL certificate and fill in the certName and certPassword fields.

**Default Value**: 8443

## certName

SSL Certificate Name. Leave blank to disable HTTPS.

## certPassword

SSL Certificate Password. Leave blank to disable HTTPS.

## disableFileSecurity

When this value is true, then any user can access any file. When this is false, file permissions are checked.

**Default Value**: false

## broadcast

When this value is true, the server's broker is broadcast to the local network for discovery by other machines. When this value is false, the broadcast service is not enabled.

**Default Value**: true

## workers

Number of Server Workers. For low end devices, this should stay at 1. For large machines, this can be set to the number of available processors to better handle massive amounts of connections.

**Default Value**: For low end devices, this is 1, for other devices, this is 2.

## updateInterval

The interval in which data is sent to links by the broker in milliseconds. This options will be going away soon.

**Default Value**: 200

## static

Configures a Static Directory Mapping. This is used to serve files and directories on the server.

Example:

```
{
  "/static": "/srv/http/static"
}
```

**Default Value**: {}
