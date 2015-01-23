# Connection Test Case

[Testing code of the following steps in dart](https://github.com/IOT-DSA/sdk-dslink-dart/blob/master/test/ds_handshake_test.dart)


## Client Private Key
**modulus** of public key and private key (Base64)
```
jjxTpOaEQO4SlZQAGYlMHIEJvz6mDWQlxUW7uMOiSe2Dg60wu8I3pnQ0HDHYTQ35rP2i80WmEbaScTVz-oITO7Lh0470_epOtuwDWezEomdP2dheiGI4jageiJ0ratZ0VqQi63thfGOaBGpG15-TUaDNRUSyB8JQlGWJrojS5lIcVsnkq129mgBOFJCUzLWBp0fwbFBve1T3cYLrPoLQgIQPINiMnokw-iRjp-C9o8cqbh1WBBQOeSxg97AD4L-0mO6NgzhXZ_jjwaJG10e9BHQkvatwU-PFivnasH_fXbyXJqs-plWCQY462Ook2xer_94gCVT8gFubBalBjluw3Q
```
**privateExponent** of private key (Base64)
```
eBMZkdZAxAe3jKrawrQTmuScc-TRjfCDqlxKM5qEQODP67Ojtn4pOM5Ux5CUx8gKhS3CCJk9rypvyj6T4GE7F2TWHCFNVaYeOXJZCetFvMx2rrNoar5we7X3wODeLF1K7XG3QRBxpe73sM5_a7x9Q6X6ZIWvvbkfCYgGiV9cm65nps4UTTmBnnh6GXcDFx9uPD5uPeMowtZh-bHzFfEYCj9dBaPfa9mQhHtqFODH8TpTOCDB8iPsJpl7loFmQQZzTRq6qr1UfPzRmfhJi_b_zdj8r5_gUEL8593StiXMIcPYlTnmUPRZtWjpFrlu3H4xEIMkPzowLqhp8KKotFGSCQ
```
publicExponent of public key (const value)
```
65537 (0x10001)
```

## Initialize connection , Client -> Server
**dsId**: client's dsId , last 43 chars are the base64 of public key modulus
```
test-pTrfpbVWb3NNAhMIXr_FpmV3oObtMVxPcNu2mDksp0M
```

client **publicKey** modulus(base64)
```
jjxTpOaEQO4SlZQAGYlMHIEJvz6mDWQlxUW7uMOiSe2Dg60wu8I3pnQ0HDHYTQ35rP2i80WmEbaScTVz-oITO7Lh0470_epOtuwDWezEomdP2dheiGI4jageiJ0ratZ0VqQi63thfGOaBGpG15-TUaDNRUSyB8JQlGWJrojS5lIcVsnkq129mgBOFJCUzLWBp0fwbFBve1T3cYLrPoLQgIQPINiMnokw-iRjp-C9o8cqbh1WBBQOeSxg97AD4L-0mO6NgzhXZ_jjwaJG10e9BHQkvatwU-PFivnasH_fXbyXJqs-plWCQY462Ook2xer_94gCVT8gFubBalBjluw3Q
```

## Initialize connection , Server -> Client
**encryptedNonce** (Base64)
```
YM2x5wEChxriLalS8tD5l2hlV6MUU-MmqbUNyDz5dUl1x8sNt7cBdh0MLc7mSb8Ohx-Q2_tW-i9fA0WQNFdWIdDZfNziUF4snFtZjez77eOSXFns4j51ZMdGXeWGRrlF5F1pGtIorFfMaofbD-QjX-VIe-TD-6QJDHVL9larXxVS2lnxY5YDhS1niHY-MXCBVUMPt9b9OOz87GTUlTu1mZJbq004mU_Du81D8j7aRNbaSIKmYWPJpoqW00yNXkADQZmVL8xVxyEApMrDF9VQMo1cNle5Tyxtvn79fF7zNE6On0JDaRg0ozP_fjV2-V_afr-OkStIWh5K_zBHfH1Xyg
```
 - server generates the encrypted nonce with following steps
   1. server generate 128 bits (16 bytes) random nonce data
     - nonce = Hex```d26538aabf9a97bcfd8bc0dd1a727c92```
   1. server generate a big number A < modulus, with the last 16 bytes same as the nonce
     - in this test case all the other bytes are just 0
     - A = Hex```d26538aabf9a97bcfd8bc0dd1a727c92```
   1. server encrypt A with client's public key
     - E = A.modPow(65537, modulus)
   1. encryptedNonce = base64(E)

**salt**, a random string
```
"0x100"
```

## Start Connection (http or ws), Client -> Server
**dsId**: client's dsId

**dsAuth**: client's proof of public key owner
```
MRxHkgT_dEszsB3kWe3HSu1Z8V1c1Z_uTvxP66-Nx0Y
```
 - client calculates the dsAuth with following steps
   - Base64 decode encryptedNonce into big integer E
   - calculate A = E.modPow(privateExponent, modulus)
   - nonce data = last 16 bytes of A (if A is less than 16 bytes, add 0 in the front)
   - encode the salt into utf8, UTF8("0x100") = Hex```3078313030```
   - concat salt and nonce bytes: H = salt+nonce = Hex```3078313030d26538aabf9a97bcfd8bc0dd1a727c92```
   - auth = base64(sha256(H))

## Start connection, Server validate Client
   server has A, salt and nonce already, use same algorithm to check if dsAuth value is same