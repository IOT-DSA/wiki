DSA has support for [Docker](https://www.docker.com/) via Docker Images.

This page assumes you have basic knowledge of Docker, and already have Docker installed.

## DGLux Server

DGLux Server is [available on Docker Hub](https://hub.docker.com/r/iotdsa/dglux-server/).

### Run a Simple Server

```bash
# Run a server that is exposed on port 8080
$ docker run -p 0.0.0.0:8080:8080 iotdsa/dglux-server
```

### Create a Server Container

```bash
# Create a container that is exposed on port 8080.
# This step only needs to be done once.
$ docker create iotdsa/dglux-server
your_container_id

# Start the container.
$ docker start your_container_id

# Stop the container.
$ docker stop your_container_id
```
