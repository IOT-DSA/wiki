To setup LDAP Authentication in DGLux Server, add the following to your server.json:

```
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
