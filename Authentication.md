To configure authentication for DGLux servers, edit the “server.json” file and set the authType parameter. Valid options are as follows:
 
* [file](#file-or-json) (formerly “json”)
* [ldap](#ldap) 
* [ldap-with-fallback](#ldap)
* [ldap-with-fallback-and-file](#ldap)
* [openId](#openId)
* [open](#open)
* [composite](#composite)

The following sections describe these options in detail.

## file or json

This is the default login mechanism, and requires no additional settings. With this option, the DGLux Server authenticates users against the “users.json” file. This file is generated automatically and contains a set of JSON objects, one for each authorized user, formatted as follows:

* `username`: Case-sensitive name of authorized user
* `password`: Hashed password for user
* `superuser`: true or false, indicates whether this user has superuser privileges.
* `metadata`: a JSON object of key/value pairs containing other information about the user.

## LDAP

For authentication using LDAP, the following options are supported:

* `ldap`: Authentication against your LDAP server.
* `ldap-with-fallback`: Authenticate first against your primary LDAP server. If this attempt fails, authenticate against a secondary LDAP server.
* `ldap-with-fallback-and-file`: Authenticate first against your primary LDAP server. If this attempt fails, authenticate against a secondary LDAP server. If authentication against the secondary server fails, authenticate against the “users.json file” as described above.

To configure the primary and secondary LDAP servers, edit the “server.json” file. Specify the settings for primary server in a JSON object names “ldap” and, if using a secondary server, define its settings in a JSON object named “ldap2.”

You can specify the following LDAP server settings in “servers.json” as key/value pairs (see DGLux Server LDAP Authentication for details):

* `url`: (Required) The full ldap:// or ldaps:// protocol address. For example: ldap://myhost.org
* `dn`: (Required) Distinguished Names as specified by the remote LDAP configuration. Also known as the BindDN.
* `password`: (Optional) LDAP Bind password
* `serverCertificate`: (Optional) Filename of the server certificate to add to trusted certificates. The file must also be placed in the _certs_ directory, and be of PEM format. This enables you to add a trusted certificate that may not have been issued by a standard CA. Only for use with secure LDAP (ldaps).
* `clientCertificate`: (Optional) Filename of the client certificate to send to the server upon request. File must also be placed in the _certs_ directory, and be of PEM format. This enables an additional layer of security to prove the system is authorized to access the secure resource. Only for use with secure LDAP (ldaps) that has been configured to require a client certificate.
* `clientCertificateKey`: (Optional) Filename of the client certificate key to use when sending the client certificate to the secure LDAP server. The file must also be placed in the _certs_ directory. This is required if a clientCertificate is specified. Otherwise it is ignored. Only for use with secure LDAP (ldaps) that has been configured to require a client certificate.
* `clientKeyPassword`: (Optional) String password used to access the clientCertificateKey if it is required. A Client certificate key may be configured to be accessible only with a password.
* `searchBase`: (Required) A list of strings of search base distinguished names used to search for the user. All search bases are checked for the user and the first match is used. To maximize performance, keep this list as short as possible. Also known as the Base DN.
* `groupAttribute`: (Optional) The groupAttribute name as defined on the LDAP server. If it is found, it is used in conjunction with the superuserGroup to determine if a user has superuser privileges. This setting defaults to “gid” if omitted.
* `superuserGroup`: (Optional) An integer or string that specifies the groupAttribute that must match the corresponding entry in the LDAP server in order for the user to be granted superuser privileges. This value defaults to “superuser,” which will generally fail to match any groupAttribute values.
* `usernameAttribute`: (Optional) The usernameAttribute name as configured in the LDAP server. This setting is used as a filter when searching the LDAP server for a username. Default is `uid` if not specified.
* `allowListUsers`: (Optional) Specifies whether the user can view and query the list of users in DGLux5. Default: false (cannot view or query)
* `searchFilter`: (Optional) An additional search filter applied to the LDAP queries particularly when searching for users or authenticating. Filters are implemented as defined in [RFC2254](https://tools.ietf.org/html/rfc2254) with the exception of extensible matching which is currently not supported.
* `metadata`: (Optional) Additional data about the user to be requested from the LDAP server, specified as key/value pairs. 

Example LDAP configuration:

```json
"authType": "ldap",
"ldap": {
  "url": "ldap://myldap.host",
  "dn": "cn=root,dc=myhost,dc=org",
  "password": "myPassword",
  "searchBase": "dc=myHost,dc=org",
  "groupAttribute": "users",
  "searchFilter": "(&(objectClass=Person)(|(sn=Jensen)(cn=Babs J*)))"
}
```

## openId

For authentication against an OpenID Connect Provider. This authentication type required configuration both locally and on the remote OpenID Connect authorization provider.

You must configure the OpenID Connect authentication provider server settings in "servers.json" as key/value pairs:

* `issuer`: (Required) Full URI to the OpenID Connect Provider. This must match the issuer (`iss`) returned by the provider for tokens.
* `clientId`: (Required) The unique client id provided by the OpenID Connect Provider. See your provider for more information.
* `secret`: (Required) The matching secret for the client ID as provided by the OpenID Connect Provider.
* `usernameKey`: (Optional) Some Open ID Connect Providers will use a non-standard key to associate the desired DSA Server username. You may optionally specify this parameter to indicate that this is the key you want the DSA Server to associate with a username when mapping a response from the Open ID Connect Provider to a DSA Server user account. By default the DSA Server will try using `preferred_username` and `sub` as usernames, in that order, from the OpenID Connect Provider.
* `scope`: (Optional) This value is a list of strings. By default the DSA Server will request the scopes: `["openid", "profile"]` The `openid` scope will always be requested as defined in the OpenID Connect standard. If you wish a different or additional scope(s) to be returned, you may specify a list of strings to use. *Note:* If you specify this, it will replace the `profile` scope, so that must be added manually if you still wish to retrieve profile scope as well as any additional scopes.

Example OpenID Connect configuration:

```json
"authType": "openId",
"openId": {
  "issuer": "https://dsa-server.auth0.com",
  "clientId": "Aliew8232_weli9os0baz",
  "secret": "LvIwnw0238seaAKJboWP023scBEapesDewabwewp323sSeBweaPwea",
  "usernameKey": "email",
  "scopes": ["profile", "email", "read:contacts"]
}
```

Configuration of remote Open ID Connect Providers is beyond the scope of this document. However in most cases the process is similar to the following:

* Add a new Open ID application.
* Locate the `clientId` and `secret` associated with that application for use in the above settings.
* Add (login) callback URL. Once the authentication provider has authorized a user, it will only permit callbacks to URLs that you specify with them. There, you must specify the DSA Server address with an additional path of `/sso/openId`. For example if your DSA Server is running on localhost:8443 you must specify: `https://localhost:8443/sso/openId` in their allowed callbacks.
* (Optionally) Add logout callback URL. When logging out, a redirect to the OpenID Connect Provider will terminate the session with them. You may then allow them to redirect back to the DSA Server page (eg: `https://localhost:8443/').

### Additional configuration

Some OpenID Connect Providers will allow you to send additional properties within a scope. There are three of particular value with DSA Server.

* `superuser`: If an authorized user's information from the OpenID Connect Provider contains the key superuser, with the value set to `true` (boolean, not string), then the user will have Superuser rights within DSA Server.
* `rootUrl`: If the authorized user's information from the OpenID Connect Provider contains the key rootUrl with a string path as a value, the user will be redirected to that path by default when logged in.
* `allowDesigner`: If an authorized user's information from the OpenID Connect Provider contains the key allowDesigner, with the value set to `false` (boolean, not string), then the user will be prevented from accessing the designer when logged in to the DSA Server.

## open
This authentication type will allow any username/password combination to log in. Superuser privileges are never granted.

## composite
This option enables you to configure a sequence of the preceding authentication methods. Specify the options in square brackets in the order you want them executed. Examples:

* composite[ldap,file]: First try the LDAP server configured in server.json. If authentication fails, try to authentication the user against the users defines in users.json.
* composite[file,open]: Try to authenticate against the users defines in users.json. If authentication fails, allow the user to log in without superuser privileges.
* composite[ldap-with-fallback,file,open]: Try the primary LDAP server, then the secondary LDAP server, next the local users.json file, and finally if that fails it will allow all connections. (Note this is the same as using composite[ldap-with-fallback-and-file,open] and is included for demonstration purposes)