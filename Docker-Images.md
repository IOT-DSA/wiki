DSA has support for [Docker](https://www.docker.com/) via Docker Images.

## DGLux Server

DGLux Server is [available on Docker Hub](https://hub.docker.com/r/iotdsa/dglux-server/).

### Run a Simple Server

```bash
# Run a server that is exposed on port 8080
$ docker run -p 0.0.0.0:8080:8080 iotdsa/dglux-server
```

### Create a Server Container

```bash
# Create a container that is exposed on port 8080
$ docker create iotdsa/dglux-server
your_container_id

# Starts the container
$ docker start your_container_id

# Stops the container
$ docker stop your_container_id
```
