This is the means for which the Node API will communicate on. The broker must implement all forms of communication.

There are 3 forms of communication:

* HTTP
* WebSocket
* Socket


## lobby.json
when dslink works as server in http and websocket mode, lobby.json is needed
```javascript
{
  "dsaId":"broker-dglogik-UeumnbhI35E/Z0IPnxk8x4f413LCnMg+CAr11Xd7NZJn75NdW+sKfwf0N4SqCybbwN7/Lg4Ns3cRmlIOThoOww==",
  "publicKey":"MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEA9j0HLTm9SUQEv12mJ/4DkdpKRZaz8lo0KCWmBFeQmf709o7gpqQ/9xWqUf4klHz+M0pQpsrNobRKjQEGMDgbRMwt+rIAmPjeJg9BcguqbUkVTqBxUt1cAKNfxbs+J0NQhPAxihD4tSHedCTEZjrmgQ3opvatrNHTPdjOxzDcwh+cUGmGt4+GoPH6uIH/1aLdOjLJwSPxJyx/O2ednXhmcIOaB7q955tA0Acz6YdVjbpy7iKGP/xh9Hl1MVh+uVvypEI8LvZjj+M44au0fDvTMffnCKHV0OjM1M4EHCbJjgPKt1L4h9EToyVANGVS/7Mt6herO3SL44iyVFj3q2r+2QIDAQAB",
  "wsDataUri":"/ws_data",
  "wsUpdateUri":"/ws_update",
  "httpDataUri":"../http/data",
  "httpUpdateUri":"../http/update",
  "minUpdateIntervalMs":200
}
```
 - publicKey
   - public key of the server
 - dsaId
   - a string with {type}-{name}-{base64(SHA512(publicKey))}
 - ****Uri, URI of dsa api endpoint, absolute uri to a different host or port is not allowed
   - wsDataUri
     - a endpoint which client send websocket dsa requests to and get responses from
   - wsUpdateUri
     - a endpoint which client send websocket dsa responses to and get requests from
   - httpDataUri
     - a endpoint which client send http dsa requests to and get responses from
   - httpUpdateUri
     - a endpoint which client send http dsa responses to and get requests from
 - minUpdateIntervalMs
   - used by clients that connect to wsUpdateUri or httpUpdateUri
   - when specified, client shouldn't send stream update to server more often than the min interval, value subscription in the client side should get cached or merged.
