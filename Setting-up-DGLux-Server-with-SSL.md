Go to your dglux server installation.

Certificate generation requires certutil. On Ubuntu install using: 
```
apt-get install libnss3-tools
```

```
mkdir certs
cd certs
certutil -N -d 'sql:./'
certutil -S -s 'CN=YourOwnCertificateName' -n 'Certificate Description' -x -t 'C,C,C' -m 1000 -v 120 -d 'sql:./' -k rsa -g 2048
```

Update your `server.json` certName to `CN=YourOwnCertificateName` as well as the NSS database password.

```
{
  "certName": "CN=YourOwnCertificateName",
  "certPassword": "password",
}
```


## add existing certificate with private key
convert pem to pkcs12
```
openssl pkcs12 -export -out server.pfx -inkey server.key -in server.crt -certfile CAcert.crt
```
add the key to database with pk12util
```
pk12util -i server.pfx -d/path/to/database -W password
```