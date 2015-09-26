## Required Components
 * HTTP Client
 * WebSocket Client
 * JSON Parser
 * Cryptography Library with ECDH curve P-256(also known as secp256r1)
 * Base64 Encoder and Decoder (URL version, without padding `=`)

## Steps
 1. Download and install a DSA broker: http://get.iot-dsa.org
 2. Implement the handshake [Protocol Communication](Protocol%20Communication)
   1. HTTP Client to initiate connection from /conn, 
   2. WebSocket Client to send and receive data from /ws
 3. Implement Methods https://github.com/IOT-DSA/docs/wiki/Methods
 4. Implement standard command line arguments of the DSLink and dslink.json
   * https://github.com/IOT-DSA/docs/wiki/startup-command
   * https://github.com/IOT-DSA/docs/wiki/dslink.json  