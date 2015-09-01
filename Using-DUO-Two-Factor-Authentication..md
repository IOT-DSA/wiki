DGLux Server provides a way to easily secure your server, beyond our authentication methods, with two-factor authentication. Current DUO is implemented, but more can certainly be added in the future.

Using DUO with DGLux Server is simple.

If you haven't already, you'll need to add Web SDK as an app from within Duo.

In your server.json, set the `twoFactorAuth` setting from `none` to `duo`.

Then add the following to your server.json.

```
"duo": {
  "host": "",
  "ikey": "",
  "skey": "",
  "akey": "",
}
```

You should be able to find `host`, `ikey` (integration key), and `skey` (secret key) from within DUO's settings for Web API. `akey` is a randomized string with at least 40 characters.

After that, just restart DGLux Server, and two-factor authentication should be enabled for login.