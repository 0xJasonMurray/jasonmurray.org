---
title: "Creating a Python Development Environment using Docker"
date: 2021-08-14T23:07:27-05:00
toc: false
images:
categories:
  - tech
tags: 
  - python
  - docker
description: Creating a Python development environment using Docker
---

# Overview

Create a standalone development environment with the latest version of Python using docker.

In this example, we are running macOS 11.5.1:

```
jemurray@phalanges:~/foo $ sw_vers
ProductName:	macOS
ProductVersion:	11.5.1
BuildVersion:	20G80
```

Which has Python 3.8.2:

```
jemurray@phalanges:~ $ /usr/bin/python3 --version
Python 3.8.2
```

With `docker` we can install the latest version of Python in an isolated container without affecting the operating system and possibly breaking a core system function.


# Examples

Run an interactive Python environment using the lightweight `Alpine Linux` base image:

```text
docker run -it python:3-alpine
```

If the image does not exist, `docker` will download the necessary files during the first execution:

```
jemurray@phalanges:~ $ docker run -it python:3-alpine
Unable to find image 'python:3-alpine' locally
3-alpine: Pulling from library/python
29291e31a76a: Already exists
7905544193a1: Already exists
f5e5def5d5f7: Already exists
cef6c1fdc9e4: Already exists
24e85e6a20ef: Pull complete
Digest: sha256:e74b38ebb56eec206443291712ea932a99bf373842cd516cc11961d8139b4f79
Status: Downloaded newer image for python:3-alpine
Python 3.9.6 (default, Aug  6 2021, 21:28:54)
[GCC 10.3.1 20210424] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

Create an interactive command-line environment which mounts a local file system directory and executes as a non-root user:

```text
docker run -v "$PWD"/foo:/foo --user nobody  -it python:3-alpine sh
```

Check the version of `python` from the CLI:

```
jemurray@phalanges:~ $ docker run -v "$PWD"/foo:/foo --user nobody  -it python:3-alpine sh
~ $ python --version
Python 3.9.6
```

The local file system is located in `~/foo` with a single file in it:

```
jemurray@phalanges:~/foo $ pwd
/Users/jemurray/foo
jemurray@phalanges:~/foo $ ls -al
total 8
drwxr-xr-x   3 jemurray  staff    96 Aug 14 23:12 .
drwxr-xr-x+ 92 jemurray  staff  2944 Aug 14 23:12 ..
-rwxr-xr-x   1 jemurray  staff    46 Aug 14 23:12 hello.py
```

The `-v` option brings the local file system into the `docker` environment:

```
~ $ cd foo/
/foo $ ./hello.py
Hello World
```

