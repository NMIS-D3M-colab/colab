## Docker for Manufacturing

Initially released in 2013, Docker is an open-source Platform as a Service (PaaS) product that provide efficiency and scalebility for software deployment via OS-level virtualization technologies. Samiliar as virtual machine (VM) which can delivery isolated software applications seperately on the same host machine, docker has a better performance cause the higher level virtulisation archietecture leading to a fewer needed resources.

![Docker vs. VM](Images/01_Docker_Container_VM.png)

By comparing to VM, the Docker platform abstracts the resource on the operation system level while VM does it on the hardware level. With this archieteccture design, the Docker will efficient use the computing resource as lightweight component but the VM need a fully load of guest operation system on the hypervisor.

### Docker Prerequistes
ere are some guides from Docker.


### Getting Started with Docker

docker cheat sheet from https://www.docker.com/sites/default/files/d8/2019-09/docker-cheat-sheet.pdf

#### Build command

Build an image from the Dockerfile in the current directory and tag the image

```sh
docker build -t myimage:1.0 .
```

Tip: There is a dot '.' seperated with a blank at the end of this command.

List all images that are locally stored with the Docker Engine

```sh
docker image ls
```

Delete an image from the local image store

```sh
docker image rm alpine:3.4
```

#### Run Docker container

Run a container from the Alpine version 3.9 image, name the running container "web" and expose port 5000 externally, mapped to port 80 inside the container.

```sh
docker container run --name web -p 5000:80 alpine:3.9
```

Stop a running container through SIGTERM
```sh
docker container stop web
```

Stop a running container through SIGKILL
```sh
docker container kill web
```

List the networks
```sh
docker network ls
```

List the running container (add --all to include stopped containers)
```sh
docker container ls
```

Delete all running and stopped containers
```docker container rm -f $(docker ps -aq)

Print the last 100 lines of a container's logs
```sh
docker container logs --tail 100 web
```

#### Share 

Pull an image from a registry
```sh
docker pull myimage:1.0
```

Retag a local image with a new image name and tag
```sh
docker tag myimage:1.0 myrepo/myimage:2.0
```

Push an image to a registry
```sh
docker push myrepo/myimage:2.0
```

### Docker Tutorial

#### Run docker container from exist repository

```sh
docker run -v /home/jhuang:/usr/share/nginx/html:ro -p 8080:80 -d nginx
```

-v /full/path/to/html/directory:/usr/share/nginx/html:ro maps the directory holding our web page to the required location in the image. The ro field instructs Docker to mount it in read-only mode. It’s best to pass Docker the full paths when specifying host directories.

-p 8080:80 maps network service port 80 in the container to 8080 on our host system.

-d detaches the container from our command line session. Unlike our previous two examples, we don’t want to interact with this container.

nginx is the name of the image.


#### Customise an exist image

Prepare an index.html file and save in the current work directory.

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to NMIS!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to NMIS Digital Team!</h1>
<p>The NMIS Digital Factroy website refer to
<a href="https://nmis-digital-factory.github.io/">NMIS Digital Factory</a>.<br/>

<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

Prepare the Dockerfile save at the same directory as the index.html file.

```sh
FROM nginx
COPY index.html /usr/share/nginx/html
```

now build the image with the command.
```sh
docker build -t nmisweb
```

run this customised image
```sh
docker run --name nmisweb_run -d -p 8080:80 nmisweb
```

#### Build a image from scratch

In this tutorial, you will create an image from a python file. This python file accept an enviromant varible 'NAME' and displayed it associate with current host.

Prepare the python file and save as name app.py in your work directory.

```python
from flask import Flask
import os
import socket

app = Flask(__name__)
@app.route("/")

def hello():
    html = "<h3>Hello {name}!</h3> <b>Hostname:</b> {hostname}<br/>"
    return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname())

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=4000)
```

Create the Dockerfile at the same directory.
```sh
# Use an official Python runtime as a parent image
FROM python:2.7-slim
WORKDIR /app
ADD . /app
RUN pip install --trusted-host pypi.python.org Flask
ENV NAME World
CMD ["python", "app.py"]
```

Build this image.

```sh
docker build -t mypyweb
```

Run this image.
```sh
docker run --name webapp -p 8081:4000 mypyweb
```

#### Create image from a c file.

This tutorial will create the hello-world image from c file. 
You can clone the hello-world repository from Github via the command.
```sh
git clone https://github.com/docker-library/hello-world.git
```

go to the hello-world directory and compile the programe with
```sh
gcc -o hello hello.c -static
```

Prepare the Dockerfile
```sh
# syntax=docker/dockerfile:1
FROM scratch
ADD hello /
CMD ["/hello"]
```

Build the image.
```sh
docker build --tag hello .
```

Run the image
```sh
docker run --name myhello hello
```

