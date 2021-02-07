---
title: "Using Bash functions to tab complete SSHing into hosts"
date: 2020-04-24T14:30:05-05:00
categories:
  - tech
tags: [til, bash, scripting]
---

SSH by tab-completing and proxying through a bastion host.  I use this in a production environment where my team SSH's into 1000's of networking devices and unix systems.  

By creating naming schemes, such as: `buildingcode-floor-systemtype-uniqid` it makes finding the host very simple.  The file can be placed in a shared folder through Dropbox, Box, Google Drive, etc., referenced by a Bash `source` statement, and used by the entire team.


Example .bashrc file:

```
# With a bastion host
function host1() {  ssh -t -A $UNIX_USER@bastion.example.com "ssh $NETWORK_USER@host1.example.com" ;}
function host2() {  ssh -t -A $UNIX_USER@bastion.example.com "ssh $NETWORK_USER@host2.example.com" ;}
function host3() {  ssh -t -A $UNIX_USER@bastion.example.com "ssh $NETWORK_USER@host3.example.com" ;}
function host4() {  ssh -t -A $UNIX_USER@bastion.example.com "ssh $NETWORK_USER@host4.example.com" ;}
# Without a bastion host
function host5() {  ssh host5.example.com ;}
```

Example run:

```
jemurray@mbp-2019:~ $ host1
Autopushing login request to phone...
Success. Logging you in...
Linux shell 4.19.0-6-cloud-amd64 #1 SMP Debian 4.19.67-2+deb10u1 (2019-09-20) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Tue Apr 21 21:42:54 2020 from 2600:6c40:7e80:2515:e4c0:4484:93e6:a62b
```


Example if the tab is pressed after typing `host<tab>`:
```
jemurray@mbp-2019:~ $ host
host      host1     host2     host3     host4     host5     hostid    hostinfo  hostname
jemurray@mbp-2019:~ $ host
```
