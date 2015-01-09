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
### HTTP handshake 
![](https://raw.githubusercontent.com/IOT-DSA/docs/master/images/http_handshake.png)

### WebSocket handshake
WebSocket connection is very similar to http mode except the headers and authentication only need to be done once.
![](https://raw.githubusercontent.com/IOT-DSA/docs/master/images/ws_handshake.png)

All Base64 encoded strings used in DSLink handshake are url and filename safe Base64 alphabet [rfc-4648](https://tools.ietf.org/html/rfc4648)
#### HTTP headers 
 - ds-id
	 - A unique string of 64-128 characters, the last 64 characters are Base64 encoded SHA384 hash of the public-key binary
 - ds-public-key
	 - Base64 encoded modulus integer of a 2048 bit RSA public key. *(the exponent of the public key is always 65537)*
 - ds-is-requester
	 - Indicates if the client is a requester
 - ds-is-responder
	 - Indicates if the client is a responder
 - ds-zone (optional)
	 - Designates the quarantine zone the client wants to be in.
 - ds-encrypted-nonce
	- Encrypted nonce that is Base64 encoded
	- Server generates a 128bit secret nonce and encrypt the nonce with client's public key
	- New nonce shouldn't overwrite the existing nonce until the client verifies its public key with ds-auth
 - ds-req-salt
	- A salt string to protect connection from replay attack, only used when ds-is-requester=true
	- Server should make sure that the salt is never reused unless connection is reset and nonce is regenerated
 - ds-resp-salt
	- A salt string to protect connection from replay attack, only used when ds-is-responder=true
 - ds-auth
	- Authentication string encoded in Base64 to prove client is a valid owner of the ds-id and public-key
	- SHA256 (UTF8Bytes (salt) + NonceBytes) *("+" here means concatenating of byte buffer)*
	- NonceBytes is decrypted from the dsa-encrypted-nonce with the client's private key
	- ds-auth is required by all http requests from client, and either ds-req-salt or sa-resp-salt is required for all http responses from the server
 - ds-req-salt-s and ds-resp-salt-s
	- salt string for short polling
 - ds-auth-s
	- short polling version of ds-auth, same algorithm as ds-auth, but uses ds-req-salt-s and ds-resp-salt-s as salt string
	- a short polling can only happen in http mode
	- when receives a short polling, server won't return any request/response, they will be merged into the request/response of the long polling

#### server-configuration content
This is an example configuration of a DSA node.	 
```javascript
{
  "id":"broker-dsa-5PjTP4kGLqxAAykKBU1MDUb0diZNOUpk_Au8MWxtCYa2YE_hOFaC8eAO6zz6FC0e",
  "public-key":"AIHYvVkY5M_uMsRI4XmTH6nkngf2lMLXOOX4rfhliEYhv4Hw1wlb_I39Q5cw6a9zHSvonI8ZuG73HWLGKVlDmHGbYHWsWsXgrAouWt5H3AMGZl3hPoftvs0rktVsq0L_pz2Cp1h_7XGot87cLah5IV-AJ5bKBBFkXHOqOsIiDXNFhHjSI_emuRh01LmaN9_aBwfkyNq73zP8kY-hpb5mEG-sIcLvMecxsVS-guMFRCk_V77AzVCwOU52dmpfT5oNwiWhLf2n9A5GVyFxxzhKRc8NrfSdTFzKn0LvDPM29UDfzGOyWpfJCwrYisrftC3QbBD7e0liGbMCN5UgZsSssOk=",
  "ws-data-uri":"../ws/data",
  "ws-update-uri":"../ws/update",
  "http-data-uri":"/http_data",
  "http-update-uri":"/http_update",
  "min-update-interval-ms":200
}
```
When client connect to server's /conn end point, sever should return its configuration JSON in the http response body

 - id
	- ds-id of the server
 - public-key
	- Base64 encoded public key of the server
 - ws-data-uri
	- An endpoint to which a client sends WebSocket nodeAPI requests to and gets responses from
	- absolute URI to a different host or port is not allowed
 - ws-update-uri
	- An endpoint to which a client sends WebSocket nodeAPI responses to and gets requests from
 - http-data-uri
	- An endpoint to which a client sends HTTP nodeAPI requests to and gets responses from
 - http-update-uri
	- An endpoint to which a client sends HTTP nodeAPI responses to and gets requests from
 - min-update-interval-ms
	- Used by clients that connect to wsUpdateUri or httpUpdateUri
	- When specified, a client shouldn't send stream updates to server more often than the minimum interval in milliseconds, value subscription in the client side should get cached or merged.