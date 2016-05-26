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
  "static": {},
  "linkConfig": {},
  "disabledLinks": [],
  "uptimeCheckUrl": null,
  "upstream": {},
  "allowAllLinks": true,
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


## static

Configures a Static Directory Mapping. This is used to serve files and directories on the server.

Example:

```
{
  "/static": "/srv/http/static"
}
```

**Default Value**: {}

## defaultPermission

Default permission setting for the root node. 
When this value is null, permission is disabled, and everything have config permission

Check [Permission-List-for-the-Root](Permission-List-for-the-Root) for details

## allowAllLinks

When value is true, all incoming dslink connection will be accepted to `/downstream`

When value is false, incoming dslink without proper authentication will be rejected unless quarantine is enabled

## quarantine
** this config has no effect when allowAllLinks is true **
When value is true, new incoming dslink without token will be put in `/sys/quarantine`

A quarantined dslink can only work as a responder. Use the `/sys/quarantine/authorize` to move a quarantined dslink to `/downstream`. 
