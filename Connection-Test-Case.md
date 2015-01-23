# Connection Test Case

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
OgYq4w8GVbwXR9YHqjRPP_Zin-6PNej2jH6KK_Bh0Amsl6dl4ATkCwdiD5yFPsNzDhJjPFm_v-dT_83jytUwrUH_IszQ2Qlw0mJ8ubn5bBZonzntDwZu_rQLLVCQEj1YsylEl5B1LDvO6ZADXISrbYh500Kts694_XLVkEA7COa_NZZvVt7MY06aLPOhVZGrv3y6bNIhnnGtxoK-Natpk7av6orZTtrNfJxZKv1U0JDy2kPc2ujsv7xJ0YMcV4Qwc3ikPRQLoDReETj5_Z1b0sd7wCza5fOPsjM5rqzIRJqY8eaA2PcidR1-JYYFkfnXhAzVe3fxt9l9ZpqTUIT5YA
```
 - server generates the encrypted nonce with following steps
   1. server generate 128 bits (16 bytes) random nonce data
     - nonce = Hex```6293688ad7c203b8702f7e06fc472402```
   1. server generate a big number A < modulus, with the last 16 bytes same as the nonce
     - in this test case all the other bytes are just 0
     - A = Hex```6293688ad7c203b8702f7e06fc472402```
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
U_9zjyQgFEXZl4SvKsLv71ES_5QXPGO-FJj8IMHEzuU
```
 - client calculates the dsAuth with following steps
   - Base64 decode encryptedNonce into big integer E
   - calculate A = E.modPow(privateExponent, modulue)
   - nonce data = last 16 bytes of A (if A is less than 16 bytes, add 0 in the front)
   - encode the salt into utf8, UTF8("0x100") = Hex```3078313030```
   - concat salt and nonce bytes: H = salt+nonce = Hex```30783130306293688ad7c203b8702f7e06fc472402```
   - auth = base64(sha256(H))

## Start connection, Server validate Client
   server has A, salt and nonce already, use same algorithm to check if dsAuth value is same