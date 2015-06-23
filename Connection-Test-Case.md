# Connection Test Case

[Testing code of the following steps in dart](https://github.com/IOT-DSA/sdk-dslink-dart/blob/master/tool/experiment/test_ds_handshake.dart)


## Client Private Key
**publicKey**, Bae64 encoded ECPoint in X9.63 format (uncompressed) 
```
BEACGownMzthVjNFT7Ry-RPX395kPSoUqhQ_H_vz0dZzs5RYoVJKA16XZhdYd__ksJP0DOlwQXAvoDjSMWAhkg4
```
**privateKey** Bae64 encoded big integer (with NO leading bit for sign)
```
M6S41GAL0gH0I97Hhy7A2-icf8dHnxXPmYIRwem03HE
```


## Initialize connection , Client -> Server
**dsId**: client's dsId , last 43 chars are the Base64 encoded SHA256 of the public key binary
```
test-s-R9RKdvC2VNkfRwpNDMMpmT_YWVbhPLfbIc-7g4cpc
```

client **publicKey** modulus(base64)
```
BEACGownMzthVjNFT7Ry-RPX395kPSoUqhQ_H_vz0dZzs5RYoVJKA16XZhdYd__ksJP0DOlwQXAvoDjSMWAhkg4
```

## Initialize connection , Server -> Client
**tempKey** (Base64)
```
BCVrEhPXmozrKAextseekQauwrRz3lz2sj56td9j09Oajar0RoVR5Uo95AVuuws1vVEbDzhOUu7freU0BXD759U
```
 - server generates a temp ECDH key pair, and send the public key's ECPoint Q to client
   -  private key d is:```rL23cF6HxmEoIaR0V2aORlQVq2LLn20FCi4_lNdeRkk```

**salt**, a random string
```
"0000"
```

## Start Connection (http or ws), Client -> Server
**dsId**: client's dsId

**dsAuth**: client's proof of public key owner
```
V2P1nwhoENIi7SqkNBuRFcoc8daWd_iWYYDh_0Z01rs
```
 - client calculates the dsAuth with following steps
   - get the sharedSecret with temp public key from server and client private key
     - sharedSecret = (tempKey.Q * privateKey.D).x;
     - sharedSecret is always 32 bytes (256 bits), add padding 0 to the left if x only has 31 bytes or less
   - encode the salt into utf8, UTF8("0000") = Hex```30303030```
   - concat salt and sharedSecret bytes: H = salt+sharedSecret = Hex```30303030116128c016cf380933c4b40ffeee8ef5999167f5c3d49298ba2ebfd0502e74e3```
   - auth = base64(sha256(H))

## Start connection, Server validate Client
   server has A, salt and nonce already, use same algorithm to check if dsAuth value is same