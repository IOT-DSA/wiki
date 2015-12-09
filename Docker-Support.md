DSA has support for [Docker](https://www.docker.com/) via Docker Images.

This page assumes you have basic knowledge of Docker, and already have Docker installed.

## DGLux Server

DGLux Server is [available on Docker Hub](https://hub.docker.com/r/iotdsa/dglux-server/).

### Create a Server Container

```bash
# Create a container that is exposed on port 8080.
# This command will output a container id to be used by other commands.
# This step only needs to be done once.
$ docker create -p 0.0.0.0:8080:8080 iotdsa/dglux-server
your_container_id

# Start the container.
# After running this command, DGLux Server will be accessible
# at http://127.0.0.1:8080
$ docker start your_container_id

# Stop the container.
$ docker stop your_container_id
```

### Building your own image

To build your own image, create a file named `Dockerfile` in a blank directory with the following content, modifying to fit your needs:

```dockerfile
FROM iotdsa/dglux-server

################################################
# Each block below is an example customization #
#   Multiple customizations can be combined.   #
################################################

# Load a project export.
# Place the project export in the directory that
# contains the Dockerfile
ADD project.zip /app
RUN unzip project.zip -d /app/files/proj && rm project.zip

# Customize the server options.
# Place a custom server.json file in the
# directory that contains the Dockerfile
ADD server.json /app
```

Then execute the following:
```bash
# Build an image with your customizations.
$ docker build -t my-dglux-server --rm=true .

# Create a container using the created image.
$ docker create -p 0.0.0.0:8080:8080 my-dglux-server
```
