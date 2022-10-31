---
title: "Proxmox Initial Setup in my Home Lab"
date: 2022-10-16T17:59:56-05:00
toc: false
images:
categories:
  - tech
tags: 
  - proxmod
  - homelab
description:
---

## Overview

I've replaced all my home lab VMware hypervisors with Proxmox. While we use `vmware` at work, and gaining experience in this environment is good. I've found the "free" tier of `vmware` for a home lab limiting and difficult to manage. Proxmox has been a pleasure to use, does not have hardware resources limits, and is a easy to keep updated. 

## Technical Details

### Assumptions and Environment

* Server Disk Layout:
  * 300G boot array
  * 10T storage array

### Create the Boot Media (using macOS)

* Download the latest image: https://www.proxmox.com/en/downloads/category/iso-images-pve
* Use balenaEtcher to write the `proxmox-ve_7.2-1.iso` image to a USB drive.

### Boot from USB Drive and Install

* Follow the directions on screen.

### Web or SSH Into the New Server

* Connect to the Web Interface: https://192.168.86.2:8006

  OR

* `ssh` to a root shell:
  ```text
  ssh root@192.168.86.2
  ```

### Enable the "free" Subscription Update Servers (CLI)

* Move the "enterprise" repos to another location in case you want to use them at a later date:
  ```text
  mv /etc/apt/sources.list.d/pve-enterprise.list ~/
  ```
* Create and edit a new `apt` repo:
  ```text
  vi /etc/apt/sources.list.d/pve-no-enterprise.list
  ```
* Add the following lines to this file:
  ```text
  #deb https://enterprise.proxmox.com/debian/pve bullseye pve-enterprise

  deb http://ftp.debian.org/debian bullseye main contrib
  deb http://ftp.debian.org/debian bullseye-updates main contrib

  # PVE pve-no-subscription repository provided by proxmox.com,
  # NOT recommended for production use
  deb http://download.proxmox.com/debian/pve bullseye pve-no-subscription

  # security updates
  deb http://security.debian.org/debian-security bullseye-security main contrib
  ```

### Update the Server (CLI)

* Run `apt` to update the server:
  ```text
  apt update
  apt upgrade
  ```

### VM Boot Media Image Location

* The default location for "cdrom" boot images is: `/var/lib/vz/template/iso`

### The End

* That is it. It just works.