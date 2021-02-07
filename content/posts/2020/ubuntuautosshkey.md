---
title: "Import SSH key from GitHub during Ubuntu install"
date: 2020-10-15T06:16:09-05:00
toc: false
images:
categories:
  - tech
tags: 
  - tech
  - ubuntu
  - ssh
  - github
---

This is a neat feature.  During a Ubuntu 20.04.x installation the initial user can automatically retrieve `ssh` keys from GitHub:

![automatic ssh key retreival](/images/githubsshkeyget.png)

Right after installation, `ssh` key authentication works as expected:

```
jemurray@mbp-2019:~ $ ssh 192.168.86.32
The authenticity of host '192.168.86.32 (192.168.86.32)' can't be established.
ECDSA key fingerprint is SHA256:zd7aT9GlQV2qsONFr2fKrx86ULQvBicSKBo2mrnUnm8.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '192.168.86.32' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 20.04.1 LTS (GNU/Linux 5.4.0-51-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Wed Oct 14 20:15:43 UTC 2020

  System load:             0.93
  Usage of /:              25.1% of 23.99GB
  Memory usage:            1%
  Swap usage:              0%
  Processes:               270
  Users logged in:         0
  IPv4 address for ens160: 192.168.86.32
  IPv6 address for ens160: 2600:1700:1391:411f:20c:29ff:fe28:bb6e


53 updates can be installed immediately.
0 of these updates are security updates.
To see these additional updates run: apt list --upgradable


Last login: Wed Oct 14 20:15:19 2020
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.
```


They also disabled password authentication by default:

```
jemurray@home-server:~$ ssh 192.168.86.32
jemurray@192.168.86.32: Permission denied (publickey).
```
