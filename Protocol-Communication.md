This is the means for which the Node API will communicate on. The broker must implement all forms of communication.

There are 3 forms of communication:

* HTTP
* WebSocket
* Socket


## handshake for http and websocket 
### http handshake 
![](https://raw.githubusercontent.com/IOT-DSA/docs/master/images/http_handshake.png)

all base64 encoded strings used in dslink hand shake are url and filename safe base64 alphabet [rfc4648](https://tools.ietf.org/html/rfc4648)
#### http headers 
 - ds-id, a id string of 64-128 characters, the last 64 characters are base64 encoded SHA384 hash of the public-key binary
 - ds-public-key, base64 encoded modulus integer of a 2048 bit rsa public key, (the exponent of the public key is always 65537)
 - ds-is-requester, whether the client is a requester
 - ds-is-responder, whether the client is a responder
 - ds-zone, optional, which quarantine zone the client wants to be in.
 - dsa-encrypted-nonce, base64 encoded encrypted nonce
    - server generate a secret nonce and encrypt the nonce with client's public key
    - new nonce shouldn't overwrite the existing nonce until the client verifies its public key with ds-auth
 - ds-req-salt, a salt string to protect connection from replay attack, only used when ds-is-requester=true
    - server should make sure the salt is never reused unless connection is reset and nonse is regenerated
    - by default req-salt will be "req0", "req1", "req2"  etc...
 - ds-resp-salt, a salt string to protect connection from replay attack, only used when ds-is-responder=true
    - by default resp-salt will be "resp0", "resp1", "resp2"  etc...
 - ds-auth, authentication string encoded in base64 to prove client is valid owner of the ds-id and public-key
    - SHA256(UTF8Bytes(salt)+NonceBytes),   "+" here means concatenating of byte buffer
      - NonceBytes is decrypted from the dsa-encrypted-nonce with the client's private key
    - ds-auth is required by all http request from client, and either ds-req-salt or sa-resp-salt is required for all http response from the server

#### server-configuration content
	 
```javascript
{
  "id":"broker-dglogik-5PjTP4kGLqxAAykKBU1MDUb0diZNOUpk_Au8MWxtCYa2YE_hOFaC8eAO6zz6FC0e",
  "public-key":"AIHYvVkY5M_uMsRI4XmTH6nkngf2lMLXOOX4rfhliEYhv4Hw1wlb_I39Q5cw6a9zHSvonI8ZuG73HWLGKVlDmHGbYHWsWsXgrAouWt5H3AMGZl3hPoftvs0rktVsq0L_pz2Cp1h_7XGot87cLah5IV-AJ5bKBBFkXHOqOsIiDXNFhHjSI_emuRh01LmaN9_aBwfkyNq73zP8kY-hpb5mEG-sIcLvMecxsVS-guMFRCk_V77AzVCwOU52dmpfT5oNwiWhLf2n9A5GVyFxxzhKRc8NrfSdTFzKn0LvDPM29UDfzGOyWpfJCwrYisrftC3QbBD7e0liGbMCN5UgZsSssOk=",
  "ws-data-uri":"../ws/data",
  "ws-update-uri":"../ws/update",
  "http-data-uri":"/http_data",
  "http-update-uri":"/http_update",
  "min-update-interval-ms":200
}
```
- when client connect to server's /conn end point, sever should return its configurationjson in the http response body
 - id, ds-id of the server
 - public-key, base64 encoded public key of the server
 - xxxx-uri, URI of dsa api endpoint, absolute uri to a different host or port is not allowed
   - ws-data-uri
     - a endpoint which client send websocket dsa requests to and get responses from
   - ws-update-uri
     - a endpoint which client send websocket dsa responses to and get requests from
   - http-data-uri
     - a endpoint which client send http dsa requests to and get responses from
   - http-update-uri
     - a endpoint which client send http dsa responses to and get requests from
 - min-update-interval-ms
   - used by clients that connect to wsUpdateUri or httpUpdateUri
   - when specified, client shouldn't send stream update to server more often than the min interval, value subscription in the client side should get cached or merged.

### websocket handshake
weksocket connection is very similiar to http mode except the headers and authantication only need to be done once
![](https://raw.githubusercontent.com/IOT-DSA/docs/master/images/ws_handshake.png)