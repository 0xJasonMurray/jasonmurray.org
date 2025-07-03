---
title: "Headless X Session with x11vnc"
date: 2021-08-16T14:47:49-05:00
toc: false
images:
categories:
  - tech
tags: 
  - x11
  - x11vnc
  - vnc
  - fluxbox
description: Setting up and running an X session on a headless remote Linux server.
---

# Overview

It's possible to securely run graphical workloads on headless remote Linux servers using `x11vnc`, `ssh`, and `xvfb`.

# Configuration

## Install Software

In this example, the software is installed on a stock Ubuntu server.

- x11vnc: The server creating the X11 session
- xvfb: The virtual frame buffer for headless systems
- fluxbox: Lightweight window manager

```text
sudo apt install x11vnc xvfb fluxbox
```

## Setup VNC Server

VNC does not encrypt traffic by default. To securely connect, we wrap the VNC session in a port forwarded `ssh` tunnel. To simplify the process, the following one-line command will setup a `ssh` tunnel, start the `x11vnc` server with `xvfb`, and setup the `fluxbox` windows manager:

```text
ssh -L 5900:localhost:5900 192.168.86.5 "x11vnc -create -env FD_PROG=/usr/bin/fluxbox -env X11VNC_CREATE_GEOM=${1:-1024x768x16} -nopw -listen 127.0.0.1 -forever"
```

# Connect

## Connect to the Remote Host

Connect to the remote host with a `vnc` client. Since the session is wrapped in a port-forwarded `ssh` tunnel, the connection is made to `localhost:5900` NOT the IP address of the remote system.

[![Image of realVNC connecting to localhost:5900](/images/2021-08-16-15-02-28.png)](/images/2021-08-16-15-02-28.png)


A full `fluxbox` X session is tunneled over `ssh` using `vnc`:

[![Image of graphical remote VNC connection to headless Linux system using realVNC](/images/2021-08-16-15-03-53.png)](/images/2021-08-16-15-03-53.png)