# Protocol

In order for DSA to be seamless, there has to be a way to discover brokers. The Broker Discover Protocol is a very simple solution to this problem.

All communications use IP address `239.255.255.230` and port `1900`.

## Packet Layout

All packets are just a textual message. There is no need for a special packet structure. Discovering brokers is a very simple and quick process.

The message format is `TYPE [ARGUMENT]`, where type is required and argument is optional.

## DISCOVER

To discover brokers on the local network, the DISCOVER message is used.

```
DISCOVER
```

## BROKER

For a broker to announce itself, it sends a BROKER message with the connection URL as the argument. It should send it in a unicast message to the address of the issuer of the DISCOVER message. Multiple brokers on a given IP address with a different port are fine.

```
BROKER http://192.168.2.53:8080/conn
```

# Connection

Links should connect to every broker it has discovered. This makes it easy for users to whitelist links once they are connected.