## Library required
 * http client 
 * websocket client
 * json parser
 * crypto library that support ECDH curve P-256 (AKA secp256r1)
 * base64 encode decode (URL version, without padding `=`)

## steps
 1. download and insall a dsa broker: https://dglogik.github.io/get-dsa/
 1. implement the handshake [Protocol Communication](Protocol%20Communication)
   1. http client to initiate connection from /conn, 
   2. websocket client to send receive data from /ws
 1. implement methods https://github.com/IOT-DSA/docs/wiki/Methods
   