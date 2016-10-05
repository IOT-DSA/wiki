Single Sign On for DGLux Server

## Enable SSO Service on the Host

Modify server.json:
```json
{
  "enableSingleSignOnServer": true
}
```

## Enable SSO Service on the Clients
```json
{
  "ssoProviderUrl": "http://my.sso.host:8080/"
}
```

SSO works by authenticating against a target server. An authorization page is shown to the user on the host, and then the user is redirected back to the client server.