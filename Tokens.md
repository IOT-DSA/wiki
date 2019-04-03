A token is necessary to create a new handshake when the broker is configured to disallow unknown DSLinks.

A token is not required for DSLinks that broker manages by its DSLink manager. If a DSLink is installed using the broker's `/sys/links` node, that DSLink is maintained by the broker and uses an automatically generated token.

## Changing Whether a Token Is Required
To change whether tokens are required:
 1. Open `server.json`.
 1. Change the `allowAllLinks` value.

* When `allowAllLinks` is true, any DSLink can connect to the broker without a token.
* When `allowAllLinks` is false, dslinks installed with the broker and dslinks that are previous connected and still registered in downstream can connect without a token, but new dslink will need a token.

## Using Quarantine
When `allowAllLinks` is set to false, there is a way to manually approve incoming DSLink connections using `quarantine` parameter in the configuration file. Set `quarantine` to true to enable this mode. When new dslink is establishing a connection, it is locked under `/sys/quarantine`. Superuser can approve connection using right-click actions on the quarantine node.

## Creating a Token

These entities can add tokens:
 - A broker user with config permissions
 - A DSLink with config permissions

To create a token, access the `/sys/tokens/{username}/` node and use the **Add Token** action.

When creating a token, use these parameters:

 - **TimeRange**: A date and time string that indicates when this token is valid. Example: `2017-03-10T00:00:00/2017-03-11T12:00:00`
 - **Count**: An integer that limits how many times this token can be used. If no number is specified, there is no limit.
 - **Managed**: A boolean value. When TRUE, the token is managed, meaning that token expiration or removal causes all DSLinks connected via this token to be disconnected and removed from the broker.

## Using a Token
On the first use, specify a token in the command line.

Dart example:
```
dart run.dart --broker http://server/conn --token RMtO6mEJmUlJfoWfofiLgjguUEpuIzWP3sXeoBNSbLIVumlw
```

When a DSLink successfully connects to the broker via the token, the broker stores dslink's `dsId`. On subsequent uses, that DSLink can connect without any token.

## Implementation of the Token in the SDK
These features need to be implemented in a DSLink SDK to enable token:

#### Parameters for /conn and /ws
A tokenHash needs to be sent to `/conn` and `/ws` endpoints.

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