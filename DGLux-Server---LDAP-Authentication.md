This documentation is now available on the main [Authentication](Authentication#ldap) page.
<!--
Setting up LDAP authentication in DGLux Server is simple.

Set your `authType` setting in the server.json to `ldap`:
```json
"authType": "ldap"
```


And add the following to your server.json:

```json
  "ldap": {
    "url": "ldap://myhost.org",
    "dn": "cn=root,dc=myhost,dc=org",
    "password": "MyPassword",
    "searchBase": "dc=myhost,dc=org",
    "groupAttribute": "gidNumber",
    "superuserGroup": 0
  }
```

`groupAttribute` specifies the attribute that defines the group of a user.
`superuserGroup` specifies the value of the attribute specified by `groupAttribute` that defines whether or not the user is a superuser. If this value is `1`, then when the value of `groupAttribute` is `1`, the user is a superuser.
-->