Go to your dglux server installation.

```
mkdir certs
certutil -N -d 'sql:./‘
certutil -S -s 'cn=dartcert' -n 'self signed for dart' -x -t 'C,C,C' \
      -m 1000 -v 120 -d 'sql:./' -k rsa -g 2048
```

Update your `server.json` certname to `CN=dartcert` as well as the NSS database password.