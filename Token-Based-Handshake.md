when broker is configured to no allow unknown dslink, a token is needed for a new handshake.

### command to start a dart dslink with token
`dart run.dart --broker http://server/conn --token RMtO6mEJmUlJfoWfofiLgjguUEpuIzWP3sXeoBNSbLIVumlw`

### conn parameter and ws parameter
a tokenHash need to be send to conn and ws end point

```
http://server/conn?dsId=test-wjN6iQTk7TOXZbHHkQDH1T2zfrPcphTxchiPvTgzbww&token=RMtO6mEJmUlJfoWfegkDI-jCG-4J2Ke1L26hX_63vHlq9zsRJbFUWWIgE8U

http://server/ws?dsId=test-wjN6iQTk7TOXZbHHkQDH1T2zfrPcphTxchiPvTgzbww&auth=kd66ixI3cvVTRpgQuHSaYN4o__JtuT-XiLE6IF695uc&token=RMtO6mEJmUlJfoWfegkDI-jCG-4J2Ke1L26hX_63vHlq9zsRJbFUWWIgE8U
```

### algorithm to generate the tokenHash
```
tokenHash = token.substring(0,16) + base64(sha256(dsId+token))
```

in the above example

token = `RMtO6mEJmUlJfoWfofiLgjguUEpuIzWP3sXeoBNSbLIVumlw`

dsId = `test-wjN6iQTk7TOXZbHHkQDH1T2zfrPcphTxchiPvTgzbww`

token.substring(0,16) = `RMtO6mEJmUlJfoWf`

base64(sha256(dsId+token))   
= `base64(sha256("test-wjN6iQTk7TOXZbHHkQDH1T2zfrPcphTxchiPvTgzbwwRMtO6mEJmUlJfoWfofiLgjguUEpuIzWP3sXeoBNSbLIVumlw"))`  
= `egkDI-jCG-4J2Ke1L26hX_63vHlq9zsRJbFUWWIgE8U`

so the final tokenHash is
```
RMtO6mEJmUlJfoWfegkDI-jCG-4J2Ke1L26hX_63vHlq9zsRJbFUWWIgE8U
```