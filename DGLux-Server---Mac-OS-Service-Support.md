DGLux Server supports the `launchd` system service facility.

## Install

The service is automatically started.

```bash
./bin/daemon.sh install-service
```

## Remove

The service is automatically stopped.

```bash
./bin/daemon.sh uninstall-service
```

## Start

```bash
launchctl load ~/Library/LaunchAgents/com.dglogik.dglux-server.plist 
```

## Stop

```bash
launchctl unload ~/Library/LaunchAgents/com.dglogik.dglux-server.plist 
```

## Caveats

To listen on port 80, run the following command, while keeping DGLux Server configured for port 8080:
```
echo "
rdr pass inet proto tcp from any to any port 80 -> 127.0.0.1 port 8080
" | sudo pfctl -ef -
```