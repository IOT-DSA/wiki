The DSA Server has many options that allow you to tweak how the server works and customize it for your own use.

Options are defined in the `server.json`

An example `server.json` is:

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
  "enableCSRFProtection": false,
  "strictFileUpload": {
    "enabled": false,
    "useClamAV": false,
    "extensions": [
      "dg5",
      "dgi",
      "crt",
      "key",
      "woff",
      "ttf",
      "gif",
      "svg",
      "png",
      "jpg",
      "xml",
      "json",
      "sql",
      "csv"
    ]
  },
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
  "enableUptimeChecker": true,
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
  "enableLogCompression": true,
  "logRotationInterval": 0,
  "enableIPv6": false,
  "dartRuntimeManagerVmFlags": [],
  "javaRuntimeManagerVmFlags": [],
  "allowPasswordChanges": true,
  "keepCustomAssets": false,
  "linkManagerEnvironment": {},
  "timeHttpRequests": false,
  "generatedCertificateSubject": "/C=US/ST=California/L=Oakland/O=Acuity Brands Inc./OU=Customers/CN=*",
  "enableCertificateGeneration": true,
  "alternativeBrokerUrl": null,
  "httpPathClassification": {},
  "corsProxyRules": "",
  "enableGit": false,
  "enableSingleSignOnServer": false,
  "maxQueueSize": 256,
  "ssoProviderUrl": null,
  "formatDg5": false,
  "allowedCorsRegexString": null,
  "loginAuditFileName": "audit.log",
  "loginAudit": false,
  "blockOutsideGuests": false,
  "customLogo": null,
  "passwordExpireAfterDays" : 0
}
```

## debug

Enable/Disable Debugging Mode

**Default Value**: false

For production site, this should always be false, debug:true may result in memory leak and bugs.

## host

IP address on the host onto which to bind the server. Using "127.0.0.1" (or "localhost") will allow connections only from the current loopback (this computer). Using "0.0.0.0" will allow connections on any interface this computer has (internal/public network, local loopback). If you have multiple adaptors/interfaces and do not want the server to be available on them all, specify the IP address of the connection on which it should be available.

**Default Value**: "0.0.0.0"

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

## certKeyName

SSL private key file name. Leave blank to disable HTTPS.

## certPassword

SSL certificate password. Set to `null` to disable HTTPS.

## enableHSTS

When this value is true, the HTTP server will always redirect to the HTTPS server, and the HTTPS server will have HSTS enabled to route requests automatically to the HTTPS server.

## enableCSRFProtection

When this value is true, the HTTP server will add specific headers and cookies to help mitigate [Cross-Site Request Forgery attacks](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)).

**Default Value**: false

## strictFileUpload

`strictFileUpload` is a configuration map that contains 3 fields. When this option is enabled, it will affect various file upload capabilities. Notably, it prevents guest users from being able to upload a file; It will limit uploads to only explicitly permitted file extensions; and possibly scan uploaded files for viruses. The configuration options are:

### enabled

When set to true, strictFileUpload is enabled. If false, it will disable to strictFileUpload checks.

**Default Value**: false

### useClamAV

When set to true, the server will attempt to find [Clam Antivirus](https://www.clamav.net/) on the system and if located, it will try to utilize this to scan any file uploads the server receives from a user. If this value is false, or the ClamAV was not found on the system, antivirus scans will be skipped, but other strictFileUpload conditions still apply if enabled.

**Default Value**: false

### extensions

An allow list of permissible file extensions (omitting the leading `.`) When strictFileUpload is enabled, the filename must end in one of these extensions or the upload will be rejected.

**Default Value**: ["dg5","dgi","crt","key","woff","ttf","gif","svg","png","jpg","xml","json","sql","csv"]

## disableFileSecurity

When this value is true, then any user can access any file. When this is false, file permissions are checked.

**Default Value**: false

## isAlwaysOffline

Indicates that a server is expected to never have a full internet connection. This will prevent the server from trying to download the list of DSLinks available in the remote repository.

**Default Value**: false

## broadcast

When this value is true, the server's broker is broadcast to the local network for discovery by other machines. When this value is false, the broadcast service is not enabled.

**Default Value**: true

## workers

Number of Server Workers. For low end devices, this should stay at 1. For large machines, this can be set up to a maximum of 128. It is recommended that you do not exceed the number of logical processors on your machine. 

**Default Value**: For single-core machines, this is 1, for other devices, this is 2.

## updateInterval

This is a legacy parameter that has no significant impact. In prior versions of the DSA protocol this value would limit updates from a Responder DSLink to no faster than this value (in milliseconds). In current versions of the protocol this parameter has no effect, but remains in the event of any legacy DSLinks still connecting to the broker.

**Default Value**: 200

## static

Configures a static directory mapping. This is used to serve files and directories on the server.

Example:

```
{
  "/static": "/srv/http/static"
}
```

**Default Value**: `{"/.well-known": "/path/to/dsa/dglux-server/.well-known"}`

## linkConfig

Each DSLink may optionally specify its own configuration parameters to use. These configuration parameters can be see under the `/sys/links/<linkName>/configs` node. If you modify one of those parameters, the value is updated in the server configuration, as opposed to directly modifying the DSLink's configuration file. This value will vary depending on the DSLinks installed, their given names and the configuration parameters they may provide. It should be modified from the DSA node tree rather than by hand.

**Default Value**: `{}`

## disabledLinks

This value is a comma separated, string list of DSLink names which are installed but currently disabled (not running on start-up). This list is managed via the `/sys/links/<linkName>/enabled` node and setting it to `false`.

**Default Value**: `[]`

## enableUptimeChecker

The server also comes with a checker which will periodically check to verify that the server is still up and running and responsive. Setting this value to false will disable the uptime checker.

**Default Value**: true

## uptimeCheckUrl

The server has a built-in checker to verify it is still running, and restart it if it goes offline or drops connections. By default the checker will attempt to connect to localhost. However if the server is bound to a different interface in the [host](#host) parameter, you will need to specify the correct URL for the server. It should end in `/ping`. This only applies when [enableUptimeChecker](#enableuptimechecker) is enabled.

Example:

```"https://169.254.100.100/ping"```

**Default Value**: null

## upstream

This value is a map of upstream broker connections that this broker will try to connect to. This should be managed via the `/sys/upstream` nodes to add or remove existing entries.

**Default Value**: `{}`

## strictTls

If this value is set to `true`, then any secure connections to an upstream broker must be passed with a valid/trusted security certificate. If the certificate provided by the upstream broker is invalid the broker will fail to establish a connection and retry (progressively backing off the connection) until a valid certificate is received. If this value is `false`, then insecure certificates are accepted, but a informational message is logged.

**Default Value**: false

## quarantine

**WARNING: This setting has _no_ effect when allowAllLinks is true**

When the value is true, a new incoming DSLink without a token will be put in `/sys/quarantine`.
A quarantined DSLink can only work as a responder. Use the `/sys/quarantine/authorize` to move a quarantined DSlink to `/downstream`.

**Default Value**: false

## allowAllLinks

When the value is true, all incoming DSLink connections will be accepted to `/downstream`.
When the value is false, an incoming DSLink without the proper authentication [Token](Tokens) will be rejected, and the connection dropped, unless quarantine is enabled.

**Default Value**: true

## defaultPermission

Default permission setting for the root node. 
When this value is `null`, permissions are disabled, and everything has the config permission.

Check [Permission List for the Root](Permission-List-for-the-Root) for details.

## useRuntimeManager

**WARNING: Setting this value has no effect on a Windows based server**

This value enables both the [useDartRuntimeManager](#usedartruntimemanager) and [useJavaRuntimeManager](#usejavaruntimemanager) on the server if applicable.

**Default Value**: false

## useDartRuntimeManager

**WARNING: Setting this value has no effect on a Windows based server**

When the value is true, the Dart Runtime Manager is used for Dart DSLinks. The Dart runtime manager reduces resource consumption by merging Dart DSLinks into a single process.

**Default Value**: false

## useJavaRuntimeManager

**WARNING: Setting this value has no effect on a Windows based server**

When the value is true, the Java Runtime Manager is used for Java DSLinks. The Java runtime manager reduces resource consumption by merging Java DSLinks into a single process.

**Default Value**: false

## passwordHasherIterations

When using `file` based [authType](#authtype), passwords are encrypted locally using PBKDF2. This value determines the number of iterations of the PBKDF2 algorithm used to encode the password.

**Default Value**: 1000

Check [Password Hasher](DGLux-Server---Password-Hasher) for more information.

## passwordHasherKeyLength

When using `file` based [authType](#authtype), This value determines the number of bytes that the encoded password should store.

**Default Value**: 32

Check [Password Hasher](DGLux-Server---Password-Hasher) for more information.

## old_passwordHasherIterations

In the event you change the default [passwordHasherIterations](#passwordhasheriterations), this value can be set as the previous value for `passwordHasherIterations`. This will enable the system to verify against the previous settings and migrate to the new settings without forcing a password reset. This option is hidden if not in use.

**Default Value**: none

Check [Password Hasher](DGLux-Server---Password-Hasher) for more information.

## old_passwordHasherKeyLength

In the event you change the default [passwordHasherKeyLength](#passwordhasherkeylength), this value can be set as the previous value for `passwordHasherKeyLength`. This will enable the system to verify against the previous setting and migrate to the new setting without forcing a password reset. This option is hidden if not in use. If the new `passwordHasherKeyLength` is less than the original value, this option is not required.

**Default Value**: none

Check [Password Hasher](DGLux-Server---Password-Hasher) for more information.

## loginRedirectPath

Determines the URI that a user is redirected to when login is complete.

**Default Value**: /

## guestLoginRedirectPath

Determines the URI that a guest user is redirected to when visiting the root of the server.

**Default Value**: null (Redirects to /login)

## authType

Determines the [authentication provider](./Authentication) to use.

### Supported Authentication Providers

- file: Securely store usernames, passwords, and metadata in the users.json file
- ldap: Use LDAP authentication.
- openId: Use Open ID Connect authentication.

**Default Value**: file

## twoFactorAuth

Determines the two factor authentication provider to use.

### Supported Two-Factor Authentication Providers

- none: Don't enable two factor authentication.
- [duo](https://github.com/IOT-DSA/docs/wiki/Using-DUO-Two-Factor-Authentication): Duo Two-Factor Authentication
- [totp](https://en.wikipedia.org/wiki/Google_Authenticator): Time based One Time Password using Google Authenticator

**Default Value**: none

## runPortChecks

When set to true, this option will verify that the configured ports for the server (HTTP and HTTPS) are valid and available for use prior to actually starting the server.

**Default Value**: true

## storageDriver

This option is available for future expansion for how data is persisted at various QOS levels. Currently only `simple` is supported.

**Default Value**: simple

## downstreamName

This value is the name of the downstream connections node. Previously releases used a downstream name of `conns`. However it is recommended to leave this as the default value, as other Requester DSLinks may make assumptions of the correct path.

**Default Value**: downstream

## loggers

The server contains a number of specialized loggers, particularly for debugging, which may be added here to retrieve verbose logging information. Some examples include "File Service" and "Execute". These would normally be advised to be enabled at the request of support.

**Default Value**: []

## proxies

This value is a Map of path (key) and URI (value) pairs. Requests to the path will be forwarded to the URI

**Default Value**: {}

## hooks

This value is designed to execute a specific command line program at various server states. Currently the only supported state is `ready` which executes when the server has finished loading. The Map contains keys of state (eg ready) and value of a list of command line programs to execute.

**Default Value**: {}

## distributionUrl

This value is the url used to check for updates of the dsa server. This value can be managed in the `/sys/config` nodes (generally should not change from default).

## linkRepositoryUrl

This value is the url used to check for updates for any of the DSA Links installed via repository. This value can be managed in the `/sys/config` nodes (generally should not change from default).

## serverVmFlags

This value is a list of flags to add to the server when being started. They only apply to the DSA server and not any managed links.

**Default Value**: []

## userTimeout

Number of minutes of user inactivity (nothing being loaded from the server) after which session times out. This is a general setting, cannot be set per user.

**Default Value**: 525600 (1 year)

## allowBrowserCaching

When enabled, this value will add Cache-Control headers for 300 seconds on static files such as .dg5, images, etc.

**Default Value**: false

## serverLogLevel

Sets the log level verbosity. Levels are: `NONE`; `SEVERE`; `WARNING`; `INFO`; `FINE`; `FINEST`; `ALL`; `DEBUG`. Each level will report its level and all prior to it. (Example: INFO will log all INFO, WARNING and SEVERE messages).

**Default Value**: INFO

## enableLogCompression

If this value is true, then when log files reach approximately 5MB in size, they will be rotated and compressed. Log files will be renamed to `<logfile>.<millisecondTimeStamp>.gz`

**Default Value**: true

## logRotationInterval

This value is the number of seconds to wait before rotating log files. When this option is enabled (anything greater than 0) [enableLogCompression](#enablelogcompression) will not be used. After the specified period, a log file will be renamed to `<filename>.<number>` the higher the number the older the log file. Any files greater than 2 will be removed.

**Default Value**: 0

## enableIPv6

Toggles support for IPv6 connections. When enabled server is listening on IPv6 interface only. In order to support both IPv4 and IPv6 in the mixed environment two copies of server needs to be installed and configured.

**Default Value**: false

## dartRuntimeManagerVmFlags

When the [useRuntimeManager](#useruntimemanager) or [useDartRuntimeManager](#usedartruntimemanager) options are enabled and the platform supports the use of a runtime manager, then the flags provided here are passed to the Dart VM prior to starting the DSLink manager.

**Default Value**: []

## javaRuntimeManagerVmFlags

When the [useRuntimeManager](#useruntimemanager) or [useJavaRuntimeManager](#usejavaruntimemanager) options are enabled and the platform supports the use of a runtime manager, then the flags provided here are passed to the Java VM prior to starting the DSLink manager.

**Default Value**: []

## allowPasswordChanges

When true, this value will enable passwords to be updated via the `/change_password` URL (after the user has logged in). This is only work if supported by the current (authType)[#authtype]

**Default Value**: true

<!-- TODO: Add to top example when ready to be deployed
## passwordExpireAfterDays

This value is the number of days after which a password will expire. After logging in, the last modified date for the password is checked. If the password is past the expiry, then the user will be redirected to the password reset page.

**Default Value**: 0
-->

## keepCustomAssets

When the value is true, custom assets in www/assets are kept upon updating DGLux Server.

**Default Value**: false

## linkManagerEnvironment

This value is a map of environment variables to set when the DSLink manager is started.

**Default Value**: {}

## timeHttpRequests

If enabled, this value will cause all HTTP requests to the DSA Server to be timed and the log will be updated with the request and elapsed duration.

**Default Value**: false

## generatedCertificateSubject

If the option [enableCertificateGeneration](#enablecertificategeneration) is enabled, this is the subject used when generating the self-signed certificate.

**Default Value**: /C=US/ST=California/L=Oakland/O=Acuity Brands Inc./OU=Customers/CN=*

## enableCertificateGeneration

When this option is set to true, the server will attempt to generate self-signed SSL certificates prior to launching the server. This will set the appropriate [certName](#certname), [certKeyName](#certkeyname). If these values are not empty, then certificate generation will be skipped.

**Default Value**: true

## alternativeBrokerUrl

If you wish for all DSLink connections to be forwarded to a separate broker rather than the default broker, you would specify the URI of the alternative broker here. This was primarily used for legacy installations.

**Default Value**: null

## httpPathClassification

This value is a map of paths which may match a specific classification string. The key is the classification and the value is a list of paths which match that classification. If enabled and a requested path to the server matches a path in that classification, then that request will be treated as that type of classification request even if not matching the original hardcoded path. Currently the only supported classification is `session`. 

**Default Value**: {}

## corsProxyRules

The DSA server may also be used to proxy requests to external servers. To limit the locations which the proxy can access, a list of addresses, separated by new lines, may be added to the string.

**Default Value**: ""

## enableGit

This value will enable git version control over your project directory. When enabled, modifications to files in the project will be committed to a git repository at the same file path, and can be used in project management to see a history of changes and even revert changes.

**Default Value**: false

## enableSingleSignOnServer

In an environment where there are multiple instances of the DSA Server installed on the network, it is possible to allow all instances to refer to one primary server for authentication. When this option is enabled, this server will act as a primary server and allow other DSA Server instances to query this server for a user session on this server and if found, share it with the other instance. This requires the [ssoProviderUrl](#ssoproviderurl) be supplied to the other DSA Server instances.

**Default Value**: false

## maxQueueSize

This value is the maximum number of items stored in the queue to be sent, if the queue reaches a volume greater the behaviour will vary depending on the QOS settings (merged, dropped etc).

**Default Value**: 256

## ssoProviderUrl

When this value is supplied, it must be the URI of another DSA server instance. This server will request an existing session from the supplied server and if found grant access via that session. If no session is found, the user will be prompted to log into that server and will be redirected to this instance once successfully authenticated.

**Default Value**: null

## formatDg5

When this value is true, dglux client will save dg5 in a formatted and json with key sorted, makes it easy to track changes.

**Default Value**: false

## allowedCorsRegexString

If you wish to allow, but restrict, the access of external sites to interface with your DSA server, you can set a Regular Expression string here which much match for the external server requests to be completed.

**Default Value**: null

## loginAuditFileName

This value only applies when [loginAudit](#loginaudit) is enabled. This will be the filename, within the `/logs` directory, in which the login audits are recorded.

**Default Value**: audit.log

## loginAudit

When enabled, this option will log to the [loginAuditFileName](#loginauditfilename) any user logins, it will record the DateTime, username,  and the IP address from which the request originated. It will also log any time a user's IP address changes during an active session.

**Default Value**: false

## blockOutsideGuests

Enable this value if you wish to require a valid user login to view all projects.

**Default Value**: false

## customLogo

When the server has been configured to use a customized branded login page, this is the path to the logo image.

**Default Value**: null

## passwordExpireAfterDays

Numbe of days before user's password expire. After that user will be forced to change password upon login.

0 means the password will not expire

**Default Value**: 0