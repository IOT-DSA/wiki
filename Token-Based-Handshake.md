When a broker is configured to not allow unknown DSLinks, a token is necessary for a new handshake.

## Work flow of using a token



## Command to start a Dart DSLink with token

when dslink is running outside the broker's link manager, token can be specified in command line

dart example
```
dart run.dart --broker http://server/conn --token RMtO6mEJmUlJfoWfofiLgjguUEpuIzWP3sXeoBNSbLIVumlw
```


## implementation of the token
these features need to be implemented in a dslink sdk to enable token

#### Parameters for /conn and /ws
A tokenHash needs to be sent to /conn and /ws endpoints.

```
http://server/conn?dsId=test-wjN6iQTk7TOXZbHHkQDH1T2zfrPcphTxchiPvTgzbww&token=RMtO6mEJmUlJfoWfegkDI-jCG-4J2Ke1L26hX_63vHlq9zsRJbFUWWIgE8U

http://server/ws?dsId=test-wjN6iQTk7TOXZbHHkQDH1T2zfrPcphTxchiPvTgzbww&auth=kd66ixI3cvVTRpgQuHSaYN4o__JtuT-XiLE6IF695uc&token=RMtO6mEJmUlJfoWfegkDI-jCG-4J2Ke1L26hX_63vHlq9zsRJbFUWWIgE8U
```

#### tokenHash Generate Algorithm
```java
tokenHash = token.substring(0,16) + base64(sha256(dsId+token))
```

in the above example

```java
token = "RMtO6mEJmUlJfoWfofiLgjguUEpuIzWP3sXeoBNSbLIVumlw"

dsId = "test-wjN6iQTk7TOXZbHHkQDH1T2zfrPcphTxchiPvTgzbww"

token.substring(0,16) = "RMtO6mEJmUlJfoWf"
base64(sha256(dsId+token))   
= base64(sha256("test-wjN6iQTk7TOXZbHHkQDH1T2zfrPcphTxchiPvTgzbwwRMtO6mEJmUlJfoWfofiLgjguUEpuIzWP3sXeoBNSbLIVumlw"))
= "egkDI-jCG-4J2Ke1L26hX_63vHlq9zsRJbFUWWIgE8U"
```

The final tokenHash is:
```
RMtO6mEJmUlJfoWfegkDI-jCG-4J2Ke1L26hX_63vHlq9zsRJbFUWWIgE8U
```