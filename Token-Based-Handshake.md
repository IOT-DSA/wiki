When a broker is configured to not allow unknown DSLinks, a token is necessary for a new handshake.

## Workflow of using token

#### create token

a broker user with config permission or a dslink with config permisison can access the /sys/tokens/{username}/ node
there is an add action to create tokens.

parameters:
# TimeRange: when this token is valid
# Count: how many times this token can be used, by default it allow unlimited count
# Managed: when token is managed, all dslinks connected with this token will get disconnected and removed from broker when token is expired or removed.

#### use token
token can be specified in command line

dart example
```
dart run.dart --broker http://server/conn --token RMtO6mEJmUlJfoWfofiLgjguUEpuIzWP3sXeoBNSbLIVumlw
```

when a dslink successfully connected to broker with token, the dslink's dsId will be stored on broker, and next time it can just connect without any token. 


#### token is not needed when broker is running in development mode
open server.json on server, and change `allowAllLinks` to false.

when it's true, all dslinks can connect to broker without any token.

#### dslink maintained by broker's own dslink manager
If dslink is installed from the broker's /sys/links node, that dslink will be maintained by the broker and use a auto-generated token behind the scene.

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