---
title: "Install Win11 Dev VMware Image on the Proxmox Hypervisor"
date: 2022-10-27T22:23:17-05:00
toc: false
images:
categories:
  - tech
tags: 
  - proxmox
  - microsoft
  - win11
  - virtulization
  - homelab
description:
---

## Overview

In addition to a variety of servers running critical Internet services, understanding the desktop environment is another core part of my home lab. In this guide, we will go over what it takes to install a Windows 11 development environment on a Proxmox virtualization server.  

## Details

### Assumptions and Environment

* This guide assumes there is a working Proxmox environment.
* VM hypervisor setup:
  * Proxmox version: Virtual Environment 7.2-11
  * System memory: 1T memory
  * System drive: 10T drive, the majority is dedicated ZFS storage pool

### Download the Windows 11 Dev Image

* Start by downloading the free Windows 11 development environment from Microsoft: https://developer.microsoft.com/en-us/windows/downloads/virtual-machines/
* `ssh` into the Proxmox server:
  ```text
  ssh root@192.168.86.2
  ```
* `cd` to a directory with sufficient storage to download a 45G image. In my environment I have a 10T ZFS pool where I store OS images:
  ```text
  cd /storage-pool-1/OS-images/win11-dev
  ```
* `wget` the file VMware image:
  ```text
  wget https://aka.ms/windev_VM_vmware
  ```
* `unzip` the file:
  * I had to install `unzip` first, it is not included with Proxmox: 
    ```text
    apt install unzip
    ```
  * `unzip` the file: 
    ```text
    unzip WinDev2210Eval.VMWare.zip
    ```


### Create a new VM

* Configure the VM either from the GUI or the command line with the following settings (replace 666 with the next available VM ID):
  ```text
  qm create 666 --bios ovmf --efidisk0 storage-pool-1-dir:1 --cores 4 --cpu host --machine pc-q35-7.0 --memory 32000 --name 1229-win11-2 --net0 e1000,bridge=vmbr0,firewall=1 --onboot yes --ostype win11 --ide0 storage-pool-1-dir:16 --scsihw virtio-scsi-pci --sockets 4 --memory 32000 --tpmstate0 storage-pool-1-dir:1
  ```

### Convert Win11 VMDK to QCOW2

* Use the `qemu-img` tool to convert the image from `vmdk` to `qcow2` and place it into the VM image directory. Depending on disk and CPU speed, this may take a while:
  ```text
  qemu-img convert -f vmdk /storage-pool-1/OS-images/win11-dev/WinDev2210Eval-disk1.vmdk -O qcow2 /storage-pool-1/vz/images/666/vm-666-disk-1.qcow2
  ```
* Check and remember the size of the newly created qcow2 disk image. In this example, it is 43G:
  ```
  ls -alh /storage-pool-1/vz/images/666/vm-666-disk-1.qcow2
  -rw-r--r-- 1 root root 43G Oct 30 14:11 /storage-pool-1/vz/images/666/vm-666-disk-1.qcow2
  ```
* When the VM was created, a default raw disk image was created. This is not need and should be deleted:
  ```text
  rm /storage-pool-1/vz/images/666/vm-666-disk-1.raw
  ```

### Edit the VM configuration to use the new `qcow2` image

* Edit the VM config:
  ```text
  vi /etc/pve/local/qemu-server/666.conf
  ```
* Change the line: 
  ```
  ide0: storage-pool-1-dir:666/vm-666-disk-1.raw,size=16G
  ``` 
  to (this is where you will change the image size to the file image size gathered above, in this example 43G)
  ```text
  ide0: storage-pool-1-dir:666/vm-666-disk-1.qcow2,size=43G
  ```

### Boot the VM using the Proxmox console

Start the boot process:

[![Image of Start the Win11 Dev VM Image by clicking the Start Button](/images/2022-10-30-14-18-10.png)](/images/2022-10-30-14-18-10.png)


System Booting up. It takes a while and a few reboots to to fully start up:

[![Image of boot up screen ](/images/2022-10-30-14-20-03.png)](/images/2022-10-30-14-20-03.png)

When booted, the system automatically logs in:

[![Image of the booted system automatically logs in](/images/2022-10-30-14-49-51.png)](/images/2022-10-30-14-49-51.png)


### System Information and Post Configuration

* Default Username: `user`
* Default Password: `<unset>`
* Set password: Settings -> Accounts -> Sign-In Options -> Password -> Add
* Enable remote desktop: Settings -> System -> Remote Desktop -> On -> Confirm


### Remote Desktop

Remote desktop to managed the new system:

[![Image of remote desktop ](/images/2022-10-30-15-00-04.png)](/images/2022-10-30-15-00-04.png)

### References

* https://pve.proxmox.com/wiki/Migration_of_servers_to_Proxmox_VE#Move_the_image_to_the_Proxmox_VE_Server

