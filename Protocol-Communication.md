Security is at the core of DSA. Inter-node connections in DSA are typically initiated by the Responder to the Requester. This achieves deployment flexibility and, as an added benefit, enables nodes on a LAN to establish secure, full-duplex connections to nodes on a WAN.

There are three types of node-to-node connection in DSA:

* Requester: Can browse and interact with the model of a Responder
* Responder: Can expose its model structure, stream model updates and receive commands from Requesters
* Requester + Responder: A DSBroker implements this pattern to route requests and responses between connected nodes

<!--
There are three types of transport channel bindings:

* HTTP
* WebSocket
* Socket
-->

## Handshake for HTTP and WebSocket

[A test case for the handshake algorithm](https://github.com/IOT-DSA/docs/wiki/Connection-Test-Case)

The handshake is based on [ECDH](http://en.wikipedia.org/wiki/Elliptic_curve_Diffie%E2%80%93Hellman) with the NIST recommended curve P-256, also known as secp256r1.

All base-64-encoded strings used in DSLink handshake are URL- and filename-safe Base64 alphabet [rfc-4648](https://tools.ietf.org/html/rfc4648) with no padding.
#### connection-request-json
The request uses an HTTP POST method to perform the request, in which JSON request data is posted to the server's connection end point:
```json
{
  "publicKey": "BEACGownMzthVjNFT7Ry-RPX395kPSoUqhQ_H_vz0dZzs5RYoVJKA16XZhdYd__ksJP0DOlwQXAvoDjSMWAhkg4",
  "isRequester": true,
  "isResponder": true,
  "linkData": {},
  "version": "1.1.2",
  "formats":["msgpack","json"],
  "enableWebSocketCompression": true
}
```

Query Parameters:
 - dsId
     - A unique string of 43-128 characters, the last 43 characters are an URL-safe Base64-encoded SHA256 hash of the public-key binary
     - example: "link-dataflow-s-R9RKdvC2VNkfRwpNDMMpmT_YWVbhPLfbIc-7g4cpc"
 - token
     - [Optional token for client to access predefined authorization](Tokens)

JSON parameters:
 - publicKey
     -  Base64 of a ECDH public key ECPoint encoded in X9.63 (uncompressed)
 - isRequester
     - Indicates if the client is a requester
 - isResponder
     - Indicates if the client is a responder
 - linkData
     - Extra data that a DSLink can attach for use by the requester
 - version
     - Version of DSA protocol
 - formats
     - An array of formats supported by clients. The server assumes all DSLinks support JSON, regardless of whether it is in the array. The array is ordered from most- to least-preferred format.
 - enableWebSocketCompression
     - Enables WebSocket compression.

#### server-configuration content
This is an example configuration of a DSA node.     
```json
{
  "dsId": "broker-dsa-FEuG-dsvoy3Mfh-DY4ZLqxWdcjA9mky2MyCd0DmqTMw",
  "publicKey": "BG4OYopcM2q09amKRKsc8N99ns5dybnBYG4Fi8bQVf6fKjyT_KRlPMJCs-3zvnSbBCXzS5fZfi88JuiLYwJY0gc",
  "wsUri": "/ws",
  "tempKey": "BARngwlfjwD7goZHCh_4iWsP0e3JszsvOtovn1UyPnqZLlSOyoUH1v_Lop0oUFClpVhlzsWAAqur6S8apZaBe4I",
  "salt": "0x205",
  "path": "/downstream/link",
  "version": "1.1.2",
  "format":"msgpack"
}
```
When the client connects to the server's connection endpoint, the server must return its configuration JSON in the HTTP response body.

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
    - An endpoint for HTTP connection
    - Absolute URI to a different host or port is not allowed
    - See [HTTP Queries](#http-queries)
 - tempKey
    - One time public key for ECDH
    - Base64 of a ECDH public key ECPoint encoded in X9.63 (uncompressed)
 - salt
    - A salt string to protect connection from replay attack on websocket connection
    - Server must e sure that the salt is never reused unless connection is reset and nonce is regenerated
 - path
     - The full path where the DSLink is located on the broker
 - format
     - the format for the client link to use.
 - version
     - Version of DSA protocol
     - If this is absent, connections may assume that it is communicating with a version prior to 1.0.4 (when versions were first introduced).
 - updateInterval
    - **Do not implement, only for backwards compatibility.**
    - Only affects the responder
    - When this setting specified, a responder must not send stream updates to server more often than the minimum interval in milliseconds, value subscriptions in the responder should be cached.
       - If a value subscription update is already cached, it must update the cache with the new value to prevent useless updates or updating an incorrect value.
    - This value only affects the time between two updates of the same stream.
    - If the responder does not respect the interval, the requestor might close the connection due to flooding.

### WebSockets

![](https://raw.githubusercontent.com/IOT-DSA/docs/master/images/ws_handshake.png)

Both WebSocket client and server need to make sure a message is sent to other side at least once every 60 seconds. Because network latency can cause message delay on other side, 30 to 45 seconds is the suggested minimum value.

If no message is received in 60 seconds, the WebSocket can be considered disconnected, and re-connection is necessary.

###### Sending queries
The client must send the following URL parameters:
 - dsId
    - dsId of the client
 - auth
    - Authentication string, encoded in Base64, to prove that the client is a valid owner of the dsId and publicKey
    - SHA256 (UTF8Bytes (salt) + SharedSecret ) *("+" here means concatenating of byte buffer)*
    - SharedSecret is the result of a standard ECDH with client's private key and server's one-time public key: tempKey
 - token
     - [Optional token for client to access predefined authorization](Tokens)
 - format
     - format string for the websocket data, can be either "json" or "msgpack"

###### Receiving response
- salt
    - The server creates a new salt every time for the client to hash the decrypted nonce bytes.
    - This is used for a "auth" token in a second websocket connection after the first one is disconnected



## Versions
* 1.0.1
  * Support ping message, WebSocket must be closed if no message is received from the other side for one minute or more.
* 1.0.2
  * Support binary channel.
* 1.0.3
  * Trust DSLink connected from 127.0.0.1, server doesn't send tempKey and salt, and doesn't do auth check.
* 1.0.4
  * Support ack.
  * Support linkData.
* 1.1.2
  * Support MsgPack.