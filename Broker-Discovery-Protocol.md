There is a need for a way to discover brokers on a network. Current methods for discovering devices are complicated to implement. This Broker Discovery Protocol is very simple to understand and implement.

All communication is over UDP with the address as `239.255.255.230` and the port as `1900`.

## Packet Layout

Each packet is just a textual message. We do not need a special packet structure. Discovering brokers is a very simple and quick process.

The message format is `TYPE [ARGUMENT]`, where type is required and argument is optional.

## DISCOVER

To discover brokers on the local network, you send the DISCOVER message.

```
DISCOVER
```

## BROKER

For a broker to announce itself, it sends a BROKER message with the connection url as the argument. It should send it in a unicast message to the address of the issuer of the DISCOVER message. If there are multiple brokers on a given IP with a different port, that is fine.

```
BROKER http://192.168.2.53:8080/conn
```
