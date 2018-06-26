### deploy
* a requester_auth_key string/binary is used in both cloud asb server and dsa broker
  * cloud asb server use it to generate the token
  * dsa broker use it to validate the token

### step of handshake
* browser asb client connect to https://cloudbroker/dsa-token
  * cloud broker return the token as plain text in response
* asb client connect to the broker with the url query parameter `&userToken=`

### format of token 
`permission_group;userid;timestamp;randomchars;signature`

* **permission group** : any string that's valid permission group name 
* **userid** : any string that's a valid user name
  * userid is used only for logging purpose
* **timestamp** : the timestamp when this token is generated
  * format : 2018-06-26T20:32:26.823Z
  * broker should reject the token if it's more than 30 seconds older or more than 30 seconds in the future
* **randomchars** random characters to make sure every token is unique
  * any implementation is ok, as long as it's not fixed characters
  * recommend 8 bytes of 0-9 a-z characters 
* **signature** signature of the token
  * a hmac hash on everything before the signature
  * url_base64(sha256_hmac(requester_auth_key).hash( UTF8("$permission_group;$userid;$timestamp;$randomchars")))
