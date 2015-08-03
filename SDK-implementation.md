## Libraries required
 * HTTP client 
 * Websocket client
 * JSON parser
 * Crypto library that support ECDH curve P-25(secp256r1)
 * Base64 encode decode (URL version, without padding `=`)

## Steps
 1. Download and install a DSA broker: https://dglogik.github.io/get-dsa/
 1. Implement the handshake [Protocol Communication](Protocol%20Communication)
   1. HTTP client to initiate connection from /conn, 
   2. websocket client to send receive data from /ws
 1. Implement methods https://github.com/IOT-DSA/docs/wiki/Methods
 1. Implement standard command line arguments of the DSLink and dslink.json
   * https://github.com/IOT-DSA/docs/wiki/Startup-command
   * https://github.com/IOT-DSA/docs/wiki/dslink.json  