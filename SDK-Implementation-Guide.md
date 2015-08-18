## Required Components
 * HTTP Client
 * WebSocket client
 * JSON Parser
 * Cryptography library that support ECDH curve P-25(secp256r1)
 * Base64 encoder and decoder (URL version, without padding `=`)

## Steps
 1. Download and install a DSA broker: http://get.iot-dsa.org
 1. Implement the handshake [Protocol Communication](Protocol%20Communication)
   1. HTTP client to initiate connection from /conn, 
   2. Websocket client to send and receive data from /ws
 1. Implement methods https://github.com/IOT-DSA/docs/wiki/Methods
 1. Implement standard command line arguments of the DSLink and dslink.json
   * https://github.com/IOT-DSA/docs/wiki/startup-command
   * https://github.com/IOT-DSA/docs/wiki/dslink.json  