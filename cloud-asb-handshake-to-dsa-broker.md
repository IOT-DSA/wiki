### deploy
* a requester_auth_key string/binary is used in both cloud asb server and dsa broker
  * cloud asb server use it to generate the token
  * dsa broker use it to validate the token

### step of handshake
* browser asb client connect to https://cloudbroker/dsa-conn
  * cloud broker return the token and ws url in json format
```json
{
  "wsUrl":"ws://192.168.100.1/ws",
  "asbToken":"user;rick;2018-06-26T20:32:26.823Z;9dn19bt7f;FEuG-dsvoy3Mfh-DY4ZLqxWdcjA9mky2MyCd0DmqTMw"
```
* asb client connect to the broker's /ws endpoint with the url query parameter `&asbToken=`
  * the client would skip /conn step and only connect to /ws
  * the client wont send `auth` or `dsId` in the url query when connecting to /ws

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
  * a hash on everything before the signature
  * `url_base64(SHA256 ("$permission_group;$userid;$timestamp;$randomchars;$requester_auth_key" ))`

### behavior of broker
* broker must cache the token for at least 30 seconds to make sure same token can not be used twice
* broker must allow same userId or permission group to have multiple connection (as long as their token are different)
* asb clients are always requester only and broker must not create downstream nodes for them
* broker should not allow dglux client to have qos 2 or qos 3. they should be downgraded to qos 1