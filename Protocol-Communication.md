Security is at the core of DSA. Inter-node connections in DSA are typically initiated by the Responder to the Requester. This achieves deployment flexibility as an added benefit allows for nodes on a LAN to establish secure, full-duplex connections to nodes on a WAN.

There are 3 inter-node connection types in DSA:

* Requester - Requester is able to browse and interact with the model of a Responder
* Responder - Responder is able to expose its model structure, stream model updates and receive commands from Requesters
* Requester + Responder - A DSBroker implements this pattern in an effort to route requests and responses between connected nodes

Currently there are 3 forms of transport channel bindings:

* HTTP
* WebSocket
* Socket


## Handshake for HTTP and WebSocket

[A Test Case of the handshake algorithm](https://github.com/IOT-DSA/docs/wiki/Connection-Test-Case)

The Handshake is based on [ECDH](http://en.wikipedia.org/wiki/Elliptic_curve_Diffie%E2%80%93Hellman) with the NIST recommended curve P-256, also known as secp256r1.

All Base64 encoded strings used in DSLink handshake are url and filename safe Base64 alphabet [rfc-4648](https://tools.ietf.org/html/rfc4648) with no padding
#### connection-request-json
The request uses an HTTP POST method to perform the request.
A json request data is posted to the server's connection end point:
```javascript
{
  "publicKey":"BEACGownMzthVjNFT7Ry-RPX395kPSoUqhQ_H_vz0dZzs5RYoVJKA16XZhdYd__ksJP0DOlwQXAvoDjSMWAhkg4",
  "zone":"default",
  "isRequester":true,
  "isResponder":true,
}
```

Query parameters:
 - dsId
     - A unique string of 43-128 characters, the last 43 characters are url safe Base64 encoded SHA256 hash of the public-key binary
     - example: "link-dataflow-s-R9RKdvC2VNkfRwpNDMMpmT_YWVbhPLfbIc-7g4cpc"
 
Json parameters:
 - publicKey
     -  Base64 of a ECDH public key ECPoint encoded in X9.63 (uncompressed)
 - isRequester
     - Indicates if the client is a requester
 - isResponder
     - Indicates if the client is a responder
 - zone (optional)
     - Designates the quarantine zone the client wants to be in.

#### server-configuration content
This is an example configuration of a DSA node.     
```javascript
{
  "dsId":"broker-dsa-FEuG-dsvoy3Mfh-DY4ZLqxWdcjA9mky2MyCd0DmqTMw",
  "publicKey":"BG4OYopcM2q09amKRKsc8N99ns5dybnBYG4Fi8bQVf6fKjyT_KRlPMJCs-3zvnSbBCXzS5fZfi88JuiLYwJY0gc",
  "wsUri":"/ws",
  "httpUri":"/http",
  "tempKey":"BARngwlfjwD7goZHCh_4iWsP0e3JszsvOtovn1UyPnqZLlSOyoUH1v_Lop0oUFClpVhlzsWAAqur6S8apZaBe4I",
  "salt":"0x205",
  "saltS": "1x218",
  "updateInterval":200
}
```
When client connect to server's connection end point, sever should return its configuration JSON in the http response body

 - dsId
    - dsId of the server
 - publicKey
    -  Base64 of a ECDH public key ECPoint encoded in X9.63 (uncompressed)
 - wsUri
    - An endpoint for web socket connection
    - Absolute URI to a different host or port is not allowed
    - Requires a query parameter of "auth"
      - SHA256 (UTF8Bytes (salt) + SharedSecret) *("+" here means concatenating of byte buffer)*
      - The salt is already provided within the JSON response
 - httpUri
    - An endpoint for http connection
    - Absolute URI to a different host or port is not allowed
    - See [HTTP Queries](#http-queries)
 - salt
    - A salt string to protect connection from replay attack
    - Server should make sure that the salt is never reused unless connection is reset and nonce is regenerated
 - saltS
    - salt string for short polling
 - tempKey
    - a one time public key for the ECDH
    - Base64 of a ECDH public key ECPoint encoded in X9.63 (uncompressed)
 - updateInterval
    - Only affects the responder
    - When specified, a responder shouldn't send stream updates to server more often than the minimum interval in milliseconds, value subscriptions in the responder should be cached.
       - If a value subscription update is already cached then it should update the cache with the new value to prevent useless updates or updating an incorrect value.
    - This value only affects the time between 2 updates of same stream.
    - If the responder does not respect the interval the requestor may close the connection due to flooding.

### HTTP

![](https://raw.githubusercontent.com/IOT-DSA/docs/master/images/http_handshake.png)

##### HTTP Queries
After receiving server configuration, client should send authentication data in http query string on every connection, this must also be done for websockets, however, a salt wont be returned.

###### Sending queries
The client must send the following url parameters:
 - dsId
    - dsId of the client
 - auth
    - Authentication string encoded in Base64 to prove client is a valid owner of the dsId and publicKey
    - SHA256 (UTF8Bytes (salt) + SharedSecret ) *("+" here means concatenating of byte buffer)*
    - SharedSecret is the result of a standard ECDH with client's private key and server's one time public key: tempKey

###### Receiving queries
The server sends the following headers:
- salt
    - The server creates a new salt every time for the client to hash the decrypted nonce bytes.
    - This is used for sending another query when creating the "auth" token.

### WebSockets

WebSocket connection is very similar to HTTP mode except authentication only needs to be done once.

![](https://raw.githubusercontent.com/IOT-DSA/docs/master/images/ws_handshake.png)
