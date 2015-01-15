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

All Base64 encoded strings used in DSLink handshake are url and filename safe Base64 alphabet [rfc-4648](https://tools.ietf.org/html/rfc4648)
#### connection-request-json
A json request data is posted to the server's connection end point:
```javascript
{
  "publicKey":"AIHYvVkY5M_uMsRI4XmTH6nkngf2lMLXOOX4rfhliEYhv4Hw1wlb_I39Q5cw6a9zHSvonI8ZuG73HWLGKVlDmHGbYHWsWsXgrAouWt5H3AMGZl3hPoftvs0rktVsq0L_pz2Cp1h_7XGot87cLah5IV-AJ5bKBBFkXHOqOsIiDXNFhHjSI_emuRh01LmaN9_aBwfkyNq73zP8kY-hpb5mEG-sIcLvMecxsVS-guMFRCk_V77AzVCwOU52dmpfT5oNwiWhLf2n9A5GVyFxxzhKRc8NrfSdTFzKn0LvDPM29UDfzGOyWpfJCwrYisrftC3QbBD7e0liGbMCN5UgZsSssOk=",
  "zone":"default",
  "isRequester":true,
  "isResponder":true,
}
```

Query parameters:
 - dsId
     - A unique string of 64-128 characters, the last 64 characters are Base64 encoded SHA384 hash of the public-key binary
     - example: "link-dataflow-5PjTP4kGLqxAAykKBU1MDUb0diZNOUpk_Au8MWxtCYa2YE_hOFaC8eAO6zz6FC0e"
 
Json parameters:
 - publicKey
     - Base64 encoded modulus integer of a 2048 bit RSA public key of the client link. *(the exponent of the public key is always 65537)*
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
  "dsId":"broker-dsa-5PjTP4kGLqxAAykKBU1MDUb0diZNOUpk_Au8MWxtCYa2YE_hOFaC8eAO6zz6FC0e",
  "publicKey":"AIHYvVkY5M_uMsRI4XmTH6nkngf2lMLXOOX4rfhliEYhv4Hw1wlb_I39Q5cw6a9zHSvonI8ZuG73HWLGKVlDmHGbYHWsWsXgrAouWt5H3AMGZl3hPoftvs0rktVsq0L_pz2Cp1h_7XGot87cLah5IV-AJ5bKBBFkXHOqOsIiDXNFhHjSI_emuRh01LmaN9_aBwfkyNq73zP8kY-hpb5mEG-sIcLvMecxsVS-guMFRCk_V77AzVCwOU52dmpfT5oNwiWhLf2n9A5GVyFxxzhKRc8NrfSdTFzKn0LvDPM29UDfzGOyWpfJCwrYisrftC3QbBD7e0liGbMCN5UgZsSssOk=",
  "wsUri":"../ws",
  "httpUri":"/http",
  "encryptedNonce":"MLbkJdw-eXdJe4zOEwwriEgNWuOtvsOIoBoTqW3kAx4iWMycVbn04zYSyKLtY6NeRaMY1I09-v2E_gDjRyMNSe04YCWp7KWmuWIGYRiFHwmNF9qSMI99NqxB0HJ768Rj_tLVbtbouUPiWn5oscpJTxbf7QklWsBZ6p0vC745sQbzwgBDsdXhiyiXek3FHQPJBEHBlkmiDEo5_j7_Y2FYvSeENyyoSfH2NmVgrKU5y1TGrLW6lz_5UfSH0PIEGhkNHzzBnDzR5Cka0-Rhqalvh2ygObYVXHlNihe7cZECYYSXqUkkO88y9MTr_aZYtGERjEzfsvDFtdE55gSahHM2Cw==",
  "salt":"0x205",
  "saltS": "1x218",
  "updateInterval":200
}
```
When client connect to server's connection end point, sever should return its configuration JSON in the http response body

 - dsId
    - dsId of the server
 - publicKey
    - Base64 encoded public key of the server
 - wsUri
    - An endpoint for web socket connection
    - Absolute URI to a different host or port is not allowed
    - Requires a query parameter of "auth"
      - SHA256 (UTF8Bytes (salt) + NonceBytes) *("+" here means concatenating of byte buffer)*
 - httpUri
    - An endpoint for http connection
    - Absolute URI to a different host or port is not allowed
    - See [queries]
 - salt
    - A salt string to protect connection from replay attack
    - Server should make sure that the salt is never reused unless connection is reset and nonce is regenerated
 - saltS
    - salt string for short polling
 - encryptedNonce
    - Encrypted nonce that is Base64 encoded
    - Server generates a 128bit secret nonce and encrypt the nonce with client's public key
 - updateInterval
    - Only affects the responder
    - When specified, a responder shouldn't send stream updates to server more often than the minimum interval in milliseconds, value subscriptions in the responder should be cached.
       - If a value subscription update is already cached then it should update the cache with the new value to prevent useless updates or updating an incorrect value.
    - This value only affects the time between 2 updates of same stream.
    - If the responder does not respect the interval the requestor may close the connection due to flooding.

### HTTP

![](https://raw.githubusercontent.com/IOT-DSA/docs/master/images/http_handshake.png)

##### HTTP Queries
After receiving server configuration, client should send authentication data in http query string on every connection
 - dsId
    - dsId of the client
 - auth
    - Authentication string encoded in Base64 to prove client is a valid owner of the dsId and publicKey
    - SHA256 (UTF8Bytes (salt) + NonceBytes) *("+" here means concatenating of byte buffer)*
    - NonceBytes is decrypted from the encryptedNonce with the client's private key
    - auth is required by all http requests from client, and either reqSalt or respSalt is required for all http responses from the server
 - authS
    - short polling version of auth, same algorithm as auth, but uses reqSaltS and respSaltS as salt string
    - a short polling can only happen in http mode
    - when receives a short polling, server won't return any request/response except a new reqSaltS/respSaltS string, other data will be merged into the request/response of the long polling

### WebSockets

WebSocket connection is very similar to HTTP mode except authentication only needs to be done once.

![](https://raw.githubusercontent.com/IOT-DSA/docs/master/images/ws_handshake.png)
