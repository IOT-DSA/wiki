The DGLux Server has many options that allow you to tweak how the server works and customize it for your own use.

Options are defined in the server.json

An example server.json is:

```json
{
  "debug": false,
  "port": 8080,
  "httpsport": 8443,
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

## httpsport

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
