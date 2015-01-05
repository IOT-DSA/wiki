This is the means for which the Node API will communicate on. The broker must implement all forms of communication.

There are 3 forms of communication:

* HTTP
* WebSocket
* Socket


## lobby.json
when dslink works as server in http and websocket mode, lobby.json is needed
```javascript
{
  "dsaId":"broker-dglogik-5PjTP4kGLqxAAykKBU1MDUb0diZNOUpk_Au8MWxtCYa2YE_hOFaC8eAO6zz6FC0e",
  "publicKey":"AIHYvVkY5M_uMsRI4XmTH6nkngf2lMLXOOX4rfhliEYhv4Hw1wlb_I39Q5cw6a9zHSvonI8ZuG73HWLGKVlDmHGbYHWsWsXgrAouWt5H3AMGZl3hPoftvs0rktVsq0L_pz2Cp1h_7XGot87cLah5IV-AJ5bKBBFkXHOqOsIiDXNFhHjSI_emuRh01LmaN9_aBwfkyNq73zP8kY-hpb5mEG-sIcLvMecxsVS-guMFRCk_V77AzVCwOU52dmpfT5oNwiWhLf2n9A5GVyFxxzhKRc8NrfSdTFzKn0LvDPM29UDfzGOyWpfJCwrYisrftC3QbBD7e0liGbMCN5UgZsSssOk=",
  "wsDataUri":"/ws_data",
  "wsUpdateUri":"/ws_update",
  "httpDataUri":"../http/data",
  "httpUpdateUri":"../http/update",
  "minUpdateIntervalMs":200
}
```
all base64 encoding use URL and Filename Safe Alphabet of [rfc4648](https://tools.ietf.org/html/rfc4648)
 - publicKey
   - base64 encoded public key of the server
 - dsaId
   - a string with {type}-{name}-{base64(SHA384(publicKey))}
 - xxxxUri, URI of dsa api endpoint, absolute uri to a different host or port is not allowed
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
