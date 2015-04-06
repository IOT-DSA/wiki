## when /conn fails
keep retry until it success

retry interval should be short at first, then increase to 60 seconds

1s 2s 3s ...59s  60s 60s 60s .....

## when /ws fails
start /http connection if it doesn't exist

still keep retry ws

when ws get re-connected, kill the http connection if it exists 

use the same interval rule for /conn re-connection

when connection get a 401 authentication failed error, re-start /conn

### enhancement for ws reconnection

when ws is connected but http connection already exists, don't kill http at once, but wait for 30 seconds to make sure the ws connection is stable, not on and off. during these 30 seconds, don't send the initial {} data to broker, so broker will keep using the existing http connection.

## when /http fails

if it's 401 error, restart /conn, 

otherwise just keep re-send same data to server

