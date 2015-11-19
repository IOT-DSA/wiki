On November 19th of 2015, the system that is used for SSL changed.

## Servers installed on or after November 19th, 2015

You will need two PEM files:
- A file that contains all certificates from the root authority your certificate.
- A file that contains your encrypted private key.

Place these files in the `certs` directory of your DGLux Server installation (the `dglux-server` folder). If you do not have a `certs` directory, create it.

Add the following to your `server.json` file, filling in the correct details:

```json
  "certName": "certificate.pem",
  "certKeyName": "key.pem",
  "certPassword": "mypassword",
```

The configuration above would look for the certificate file at `dglux-server/certs/certificate.pem`, the key file at `dglux-server/certs/key.pem` and decrypt the certificate with the password `mypassword`.

## Servers installed before November 19th, 2015
Go to your dglux-server installation folder. Normally it is located in: `/opt/dsa/dglux-server/`

Certificate generation requires certutil. On Ubuntu install using: 
```
apt-get install libnss3-tools
```

```
cd /opt/dsa/dglux-server/
mkdir certs
cd certs
certutil -N -d 'sql:./'
certutil -S -s 'CN=YourOwnCertificateName' -n 'Certificate Description' -x -t 'C,C,C' -m 1000 -v 9 -d 'sql:./' -k rsa -g 2048
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