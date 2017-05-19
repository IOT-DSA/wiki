To configure authentication for DGLux servers, edit the “server.json” file and set the authType parameter. Valid options are as follows:
 
* [file](#file-or-json) (formerly “json”)
* [ldap](#ldap) 
* [ldap-with-fallback](#ldap)
* [ldap-with-fallback-and-file](#ldap)
* [open](#open)
* [composite](#composite)

The following sections describe these options in detail.

## file or json

This is the default login mechanism, and requires no additional settings. With this option, the DGLux Server authenticates users against the “users.json” file. This file is generated automatically and contains a set of JSON objects, one for each authorized user, formatted as follows:

* username: Case-sensitive name of authorized user
* password: Hashed password for user
* superuser: true or false, indicates whether this user has superuser privileges.
* metadata: a JSON object of key/value pairs containing other information about the user.

## LDAP

For authentication using LDAP, the following options are supported:

* ldap: Authentication against your LDAP server.
* ldap-with-fallback: Authenticate first against your primary LDAP server. If this attempt fails, authenticate against a secondary LDAP server.
* ldap-with-fallback-and-file: Authenticate first against your primary LDAP server. If this attempt fails, authenticate against a secondary LDAP server. If authentication against the secondary server fails, authenticate against the “users.json file” as described above.

To configure the primary and secondary LDAP servers, edit the “server.json” file. Specify the settings for primary server in a JSON object names “ldap” and, if using a secondary server, define its settings in a JSON object named “ldap2.”

You can specify the following LDAP server settings in “servers.json” as key/value pairs (see DGLux Server LDAP Authentication for details):

* url: (Required) The full ldap:// or ldaps:// protocol address. For example: ldap://myhost.org
* dn: (Required) Distinguished Names as specified by the remote LDAP configuration
* password: (Optional) LDAP user password
* searchBase: (Required) A list of strings of search base distinguished names used to search for the user. All search bases are checked for the user and the first match is used. To maximize performance, keep this list as short as possible. 
* groupAttribute: (Optional) The groupAttribute name as defined on the LDAP server. If it is found, it is used in conjunction with the superuserGroup to determine if a user has superuser privileges. This setting defaults to “gid” if omitted.
* superuserGroup: (Optional) An integer or string that specifies the groupAttribute that must match the corresponding entry in the LDAP server in order for the user to be granted superuser privileges. This value defaults to “superuser,” which will generally fail to match any groupAttribute values.
* usernameAttribute: (Optional) The usernameAttribute name as configured in the LDAP server. This setting is used as a filter when searching the LDAP server for a username.
* allowListUsers: (Optional) Specifies whether the user can view and query the list of users in DGLux5. Default: false (cannot view or query)
* metadata (Optional) Additional data about the user to be requested from the LDAP server, specified as key/value pairs. 

## open
This authentication type will allow any username/password combination to log in. Superuser privileges are never granted.

## composite
This option enables you to configure a sequence of the preceding authentication methods. Specify the options in square brackets in the order you want them executed. Examples:

* composite[ldap,file]: First try the LDAP server configured in server.json. If authentication fails, try to authentication the user against the users defines in users.json.
* composite[file,open]: Try to authenticate against the users defines in users.json. If authentication fails, allow the user to log in without superuser privileges.
* composite[ldap-with-fallback,file,open]: Try the primary LDAP server, then the secondary LDAP server, next the local users.json file, and finally if that fails it will allow all connections. (Note this is the same as using composite[ldap-with-fallback-and-file,open] and is included for demonstration purposes)