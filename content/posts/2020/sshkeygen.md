---
title: "Generating and using SSH keys"
date: 2020-08-10T09:05:07-04:00
toc: false
images:
categories:
  - tech
tags: 
  - ssh
  - publickey
  - ssh-copy-id
  - ssh-agent
  - ssh-add
  - rsa
---

# Summary

SSH keys, a public key cryptographic system, secures network communication and replaces plain text password authentication on remote systems.  SSH is widely used as the default service to connect to shell accounts, switches, routers, git repositories, serial consoles, etc.  Many of these systems require ssh keys to operate, creating the keys varies depending on the operating systems and the ssh software installed.  Directions for unix-based systems, including macOS, are detailed below.

## Command summary

Create and copy keys to a remote host:

```
ssh-keygen
ssh-copy-id <remote host>
```

# Details

Use `ssh-keygen` to create the key.  The default options will be safe for the vast majority of users:

```
jemurray@shell:~$ ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/jemurray/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/jemurray/.ssh/id_rsa.
Your public key has been saved in /home/jemurray/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:5WjnOPnQwOsulQqddNpnEeNzROX2N9g4UEgrwu9DCJY jemurray@shell
The key's randomart image is:
+---[RSA 2048]----+
|          .ooo.  |
|      o   o.+.   |
|     E o o.*  o  |
|    . o.=+= o.+. |
|     o *S+o+ + oo|
|    . +.*Oo   . o|
|     . o*=o      |
|      o. +.      |
|       oo .      |
+----[SHA256]-----+
```

The new keys are stored in the `~/.ssh` directory:

```
jemurray@shell:~$ ls -al .ssh/id_rsa*
-rw------- 1 jemurray jemurray 1876 Jun 16 13:07 .ssh/id_rsa
-rw-r--r-- 1 jemurray jemurray  396 Jun 16 13:07 .ssh/id_rsa.pub
```

Use `ssh-copy-id` to facilitate copying the ssh public key to remote servers.  While `scp` can manually move the keys around, `ssh-copy-id` eliminates the complexity of creating directories, fixing permissions, and reduces accidental misconfiguration.   

```
jemurray@shell:~$ ssh-copy-id sdf.org
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/jemurray/.ssh/id_rsa.pub"
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
jemurray@sdf.org's password:

Number of key(s) added: 1

Now try logging into the machine with:   "ssh 'sdf.org'"
and check to make sure that only the key(s) you wanted were added.
```


Validate the newly created key is working:

```
jemurray@shell:~$ ssh sdf.org
Enter passphrase for key '/home/jemurray/.ssh/id_rsa':
 *
 * Wed Jun  3 23:41:54 UTC 2020
 *
```

Review the new public key `ssh-copy-id` pushed to the remote server:

```
jemurray@sdf:~ $ cat .ssh/authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC19FBRJBaiuE3uJVkRdL8CjFnQEDH/ysN2nyyn7LB/Y7dLSNnL1HWDt87Jnnc430Nv+4J7isaQdaHaG4bgeDGQyDBD9NYpmI1r6+OM1MK80dZ+Mv1Gtn7ppA/m23NuSVzVQze8arTBw0bzj90W+ro75XxjaZ+VS0VNUOdHgiDKuRs/E7Fqb52tVivB0+6BqgA9MwGN3Jm2PM0IhQFx/eBKy6glQDqotzrDw+du56GCGlFbzv8unBD6OQ2yQSyJ6ZdjaXZvHQvmPpIgThqCnqrM97Kb6/NwdYpJyfwuDeOycyPX9jYgssCqTOjazEA0mxm280nGNIceQUgD4sR6u4Pb jemurray@shell
```

`ssh-agent` saves the key password allowing remote authentication without having to type a password every time:

```
jemurray@shell:~$ ssh-agent bash

jemurray@shell:~$ ssh-add

Enter passphrase for /home/jemurray/.ssh/id_rsa:
Identity added: /home/jemurray/.ssh/id_rsa (jemurray@shell)

jemurray@shell:~$ ssh sdf.org
 *
 * Wed Jun  3 23:41:54 UTC 2020
 *
 ```