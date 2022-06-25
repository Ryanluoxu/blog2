---
title: Docker Notes
date: 2018-05-13 13:31:03
categories: CICD
tags: [docker, notes, CICD]
---

I want to install Jenkins in Docker container. And find out that Docker is an amazing tool and containerization is worthy to really be understood.

Refereces:
- [Docker Tutorial](http://www.runoob.com/docker/docker-install-nginx.html)
- [Digital Ocean Tutorial](https://www.digitalocean.com/community/tutorials/)

<!--more-->

# Docker Basic
## basic command
```bash
docker run ubuntu:15.10 /bin/echo "Hello world" 
#ubuntu:15.10 is an image

docker run -i -t ubuntu:15.10 /bin/bash
# -t:make a terminal in the container。
# -i:allow interaction with STDIN

docker run -d ubuntu:15.10 /bin/sh  # get container Id
docker ps
docker logs <CONTAINER_ID>
docker stop <CONTAINER_ID>
docker command --help
docker pull training/webapp # get image

docker run -d -P training/webapp python app.py
# -d:run container at the background
# -P:expose the container port to our server port
docker run -d -p 5000:5000 training/webapp python app.py

docker port <CONTAINER_ID>
docker logs -f <CONTAINER_ID>
# -f:use dokcer logs to show output like tail -f

docker top <CONTAINER_ID>
docker inspect <CONTAINER_ID>   # get a JSON
docker start <CONTAINER_ID>
docker rm <CONTAINER_ID>  
```
## image (image-class, container-instance)
### basic
```bash
docker images # list the images
docker pull ubuntu:13.10    # then use this image to run a container
docker search httpd
docker pull httpd
docker run httpd
```
### update image
```bash
docker run -t -i ubuntu:15.10 /bin/bash # create a container
apt-get update
exit
docker commit -m="update" -a="author" <CONTAINER_ID> boot/ubuntu:v2 # container name
docker images
docker run -t -i boot/ubuntu:v2 /bin/bash
```
### create image
Dockerfile
```
FROM    centos:6.7
MAINTAINER      Fisher "fisher@sudops.com"

RUN     /bin/echo 'root:root' |chpasswd
RUN     useradd ryan
RUN     /bin/echo 'ryan:ryan' |chpasswd
RUN     /bin/echo -e "LANG=\"en_US.UTF-8\"" >/etc/default/local
EXPOSE  22
EXPOSE  80
CMD     /usr/sbin/sshd -D
```
```bash
# build an image based on Dockerfile
docker build - < Dockerfile -t ryan/centos:6.7
# REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
# ryan/centos         6.7                 2d1b0607dc75        48 seconds ago      191 MB

## add a new tag to the image
docker tag <CONTAINER_ID> boot/centos:dev

```

## container port exposing
```bash
docker run -d -p 127.0.0.1:5001:5000 training/webapp python app.py
# 127.0.0.1:5001 can visit docker container

docker run -d -P --name <container_name> training/webapp python app.py
```

# Docker Sample
## Install Nginx
```bash
# make dir for nginx
mkdir -p ~/nginx/www ~/nginx/wwwlogs ~/nginx/conf

# get nginx image
docker search nginx
docker pull nginx

# get container
docker run --name docker-nginx -p 80:80 nginx
docker rm docker-nginx

# detach the container to allow it to run independently
docker run --name docker-nginx -p 80:80 -d nginx

# Building a Web Page to Serve on Nginx, create index page to replace the default landing page
mkdir -p ~/docker/docker-nginx/html
vi ~/docker/docker-nginx/html/index.html    # as per content below

# website content: link files in container -> files in server
## Nginx container index page : /usr/share/nginx/html
docker run --name docker-nginx -p 80:80 -d -v ~/docker/docker-nginx/html:/usr/share/nginx/html nginx

```
### index.html
```
<html>
  <head>
    <link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.5/css/bootstrap.min.css" rel="stylesheet" integrity="sha256-MfvZlkHCEqatNoGiOXveE8FIwMzZg4W85qfrfIFBfYc= sha512-dTfge/zgoMYpP7QbHy4gWMEGsbsdZeCXz7irItjcC3sPUFtf0kuFbDz/ixG7ArTxmDjLXDmezHubeNikyKGVyQ==" crossorigin="anonymous">
    <title>Docker nginx sample</title>
  </head>
  <body>
    <div class="container">
      <h1>Hello NginX</h1>
      <p>This nginx page is brought to you by Docker</p>
    </div>
  </body>
</html>
```

### customize Nginx config file
```bash
docker cp docker-nginx:/etc/nginx/conf.d/default.conf ~/docker/docker-nginx/default.conf

# run with customized default.config
docker run --name docker-nginx -p 80:80 -v ~/docker-nginx/html:/usr/share/nginx/html -v ~/docker/docker-nginx/default.conf:/etc/nginx/conf.d/default.conf -d nginx
```

