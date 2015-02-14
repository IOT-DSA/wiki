The broker is a special system that handles DSLinks. All DSLinks must connect to a broker. Permissions for DSLinks are handled in the broker. The DSLink does not handle any permissions.

Root Nodes
---
These nodes are children of the root path, "/".

* conns
  * Active, fully-authenticated, DSLink connection.
* def
  * Definitions for various internal structures which get referenced by nodes.
* quarantine
  * An active DSLink connection that is semi-authenticated. It is awaiting permission from the broker before it can send/retrieve any data from the broker. The broker *can* close the connection if the DSLink attempts to make a request or send data without being fully-authenticated.
