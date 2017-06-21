The DGLux Server has many options that allow you to tweak how the server works and customize it for your own use.

Options are defined in the server.json

An example server.json is:

```json
{
  "debug": false,
  "host": "0.0.0.0",
  "port": 8080,
  "httpsPort": 8443,
  "certName": "server.pem",
  "certKeyName": "key.pem",
  "certPassword": "",
  "enableHSTS": false,
  "disableFileSecurity": false,
  "isAlwaysOffline": false,
  "broadcast": false,
  "workers": 2,
  "updateInterval": 200,
  "static": {
    "/.well-known": "/opt/dsa/dglux-server/.well-known"
  },
  "linkConfig": {},
  "disabledLinks": [],
  "uptimeCheckUrl": null,
  "upstream": {},
  "quarantine": false,
  "allowAllLinks": true,
  "defaultPermission": null,
  "useRuntimeManager": false,
  "useDartRuntimeManager": false,
  "useJavaRuntimeManager": false,
  "passwordHasherIterations": 1000,
  "passwordHasherKeyLength": 32,
  "loginRedirectPath": "/",
  "guestLoginRedirectPath": null,
  "authType": "file",
  "twoFactorAuth": "none",
  "runPortChecks": true,
  "storageDriver": "simple",
  "downstreamName": "downstream",
  "enableUptimeChecker": true,
  "observe": false,
  "loggers": [],
  "proxies": {},
  "hooks": {},
  "distributionUrl": "https://dsa.s3.amazonaws.com/dists/dists.json",
  "linkRepositoryUrl": "https://dsa.s3.amazonaws.com/links/links.json",
  "serverVmFlags": [],
  "userTimeout": 525600,
  "allowBrowserCaching": false,
  "serverLogLevel": "INFO",
  "enableIPv6": false,
  "dartRuntimeManagerVmFlags": [],
  "javaRuntimeManagerVmFlags": [],
  "allowPasswordChanges": true,
  "keepCustomAssets": false,
  "linkManagerEnvironment": {},
  "timeHttpRequests": false,
  "generatedCertificateSubject": "/C=US/ST=California/L=Oakland/O=DGLogik Inc./OU=Customers/CN=*",
  "enableCertificateGeneration": true,
  "alternativeBrokerUrl": null,
  "httpPathClassification": {},
  "brokerName": "broker-",
  "runBrokerInMain": false
}
```

## debug

Enable/Disable Debugging Mode

**Default Value**: false

For production site, this should always be false, debug:true may result in memory leak and bugs.

## port

HTTP Port to listen on. If this is less than or equal to 0, then the server does not listen on any port for HTTP.

**Default Value**: 8080

_At least one of `port` or `httpsPort` must have a valid port number assigned._

## httpsPort

HTTPS port to listen on. If this is less than or equal to 0, and/or `certName` or `certPassword` is not provided, then the server does not listen on any port for HTTPS. Ensure that if you install a custom certificate, you fill in the `certName`, `certKeyName` and `certPassword` fields.

**Default Value**: 8443

_At least one of `port` or `httpsPort` must have a valid port number assigned._

## certName

SSL certificate file name. Leave blank to disable HTTPS.

## certPassword

SSL certificate password. Set to `null` to disable HTTPS.

## certKeyName

SSL private key file name. Leave blank to disable HTTPS.

## enableHSTS

When this value is true, the HTTP server will always redirect to the HTTPS server, and the HTTPS server will have HSTS enabled to route requests automatically to the HTTPS server.

## disableFileSecurity

When this value is true, then any user can access any file. When this is false, file permissions are checked.

**Default Value**: false

## broadcast

When this value is true, the server's broker is broadcast to the local network for discovery by other machines. When this value is false, the broadcast service is not enabled.

**Default Value**: true

## workers

Number of Server Workers. For low end devices, this should stay at 1. For large machines, this can be set up to a maximum of 128. It is recommended that you do not exceed the number of logical processors on your machine. 

**Default Value**: For single-core machines, this is 1, for other devices, this is 2.

## static

Configures a static directory mapping. This is used to serve files and directories on the server.

Example:

```
{
  "/static": "/srv/http/static"
}
```

**Default Value**: `{"/.well-known": "/path/to/dsa/dglux-server/.well-known"}`

## defaultPermission

Default permission setting for the root node. 
When this value is `null`, permissions are disabled, and everything has the config permission.

Check [Permission List for the Root](Permission-List-for-the-Root) for details.

## allowAllLinks

When the value is true, all incoming DSLink connections will be accepted to `/downstream`.
When the value is false, an incoming DSLink without proper authentication will be rejected unless quarantine is enabled.

**Default Value**: true

## quarantine

** This setting has no effect when allowAllLinks is true **

When the value is true, a new incoming DSLink without a token will be put in `/sys/quarantine`.
A quarantined DSLink can only work as a responder. Use the `/sys/quarantine/authorize` to move a quarantined DSlink to `/downstream`.

**Default Value**: false

## isAlwaysOffline

Indicates that a server is expected to never have a full internet connection.

**Default Value**: false

## useDartRuntimeManager

When the value is true, the Dart Runtime Manager is used for Dart DSLinks. The Dart runtime manager reduces resource consumption by merging Dart DSLinks into a single process.

**Default Value**: false

## useJavaRuntimeManager

When the value is true, the Java Runtime Manager is used for Java DSLinks. The Java runtime manager reduces resource consumption by merging Java DSLinks into a single process.

**Default Value**: false

## guestLoginRedirectPath

Determines the URI that a guest user is redirected to when visiting the root of the server.

**Default Value**: null (Redirects to /login)

## loginRedirectPath

Determines the URI that a user is redirected to when login is complete.

**Default Value**: /

## authType

Determines the authentication provider to use.

### Supported Authentication Providers

- file: Securely store usernames, passwords, and metadata in the users.json file
- [ldap](https://github.com/IOT-DSA/docs/wiki/DGLux-Server---LDAP-Authentication): Use LDAP authentication.

**Default Value**: file

## twoFactorAuth

Determines the two factor authentication provider to use.

### Supported Two-Factor Authentication Providers

- none: Don't enable two factor authentication.
- [duo](https://github.com/IOT-DSA/docs/wiki/Using-DUO-Two-Factor-Authentication): Duo Two-Factor Authentication

**Default Value**: none

## enableIPv6

Toggles support for IPv6 connections.

**Default Value**: false

## keepCustomAssets

When the value is true, custom assets in www/assets are kept upon updating DGLux Server.

**Default Value**: false


## formatDg5

When this value is true, dglux client will save dg5 in a formatted and json with key sorted, makes it easy to track changes.

**Default Value**: false

## userTimeout

Number of minutes of user inactivity (nothing being loaded from the server) after which session times out. This is a general setting, cannot be set per user.

**Default Value**: 525600m (1 year)