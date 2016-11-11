To apply an HTTP proxy on Linux, before starting any DSA component, run the following:
```bash
export http_proxy=http://my.proxy.com/
export https_proxy=$http_proxy
```

To apply this to DGLux Server, just stop the server, and run the start command after setting the proxy:
```bash
export http_proxy=http://my.proxy.com/
export https_proxy=$http_proxy
./bin/daemon.sh start
```
