---
title: "Common docker commands"
date: 2021-01-26T08:35:12-06:00
toc: false
images:
categories:
  - tech
tags: 
  - docker
---

# Overview

As a network architect, more and more software I use on a regular basis or within my lab environment is deployed via `docker`.   Since I don't use `docker` on a daily basis, remembering the basic commands quickly falls out of my memory after after a few weeks or months pass.

This is my place to store and reference commonly used `docker` commands.

# The Commands

## Display Container / Image Information

List docker images:

```
sudo docker images
```

List previously run images:

```
sudo docker ps --all
```

Display ID of last container run:

```
sudo docker ps -lq
```



## Run Containers

Run container detached and export container port 21 to host port 21:

```
# -p HostPort:ContainerPort
sudo docker run -d -p 21:21 opencanary opencanaryd '--start'
```

Run an interactive `/bin/bash` shell within a container:

```
sudo docker run -it opencanary /bin/bash
```

Run docker container with custom (override) `ENTRYPOINT`:

```
sudo docker run -it --entrypoint '/bin/bash' opencanary
```


## Modify Containers / Images

Display docker image configuration: 

```
sudo docker image inspect 4f13dff52540
```

Change `ENTRYPOINT` of docker image and commit changes:

```
# Manually entering ID
sudo docker commit --change='ENTRYPOINT ["opencanaryd", "--start"]' e805705a3827 opencanary:latest

# Using ID of last run container
sudo docker commit --change='ENTRYPOINT ["opencanaryd", "--start"]' $(sudo docker ps -lq) opencanary:latest


```



## Export Containers

Export a container to `tar` file:

```
sudo docker save opencanary > opencanary.tar
```