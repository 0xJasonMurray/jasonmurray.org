---
title: "Generating a password hash on Linux from the command line"
date: 2020-06-20T11:42:04-05:00
toc: false
images:
tags: 
  - mkpasswd
  - linux
  - passwd
---

# Summary

The command `mkpasswd` generates a password hash from the command line.  We can use the resulting output in any application where an md5, des, or sha hash is required.    

# Details

Running `mkpasswd` with the options `-m help` displays the available hash methods:

```
jemurray@linux-host:~$ mkpasswd -m help
Available methods:
des	standard 56 bit DES-based crypt(3)
md5	MD5
sha-256	SHA-256
sha-512	SHA-512ZZ
```

Generate the SHA-512 hash with the password 'test':

```
jemurray@linux-host:~$ mkpasswd -m SHA-512
Password:
$6$DIv5K.HO$YhcGCcZGEipD6gmopK.rCYNTfUhNV5hsy9/LhT89odM0jd/lIyGgOX5l.v1hRFV.EDXeK/IRpYQNXgv0inmW50
```

In this example, the hash is added to a `puppet` manifest for managing user accounts:


```
	manageusers::add_user { jemurray:
		name    => "Jason E. Murray",
		uid      => "666",
		password => '$6$DIv5K.HO$YhcGCcZGEipD6gmopK.rCYNTfUhNV5hsy9/LhT89odM0jd/lIyGgOX5l.v1hRFV.EDXeK/IRpYQNXgv0inmW50',
		shell => "/bin/bash",
		groups => ['sudo', 'jemurray'],
		sshkeytype => "ssh-rsa",
		sshkey => "AAAAB3NzaC1yc2EAAAADAQABAAABAQDeC4gqn+//nDrGAPS3Mj6IVf/U/oae4ge6m5HgO8mZhfpfHSzRFuRainH0iht+BtZO3dlAjRVchB8087hGt2h2yQM5yNlXW1Qh8YnmzPwAKRzmOI9ry3TZv+SSRbn3IUcdCBTn
PLKUszRftK00WjQypVtJLZHkqHFdH7Z/BDFrid1YvI6E3vKmbZUTElYmJYdu67Mg7T8nm6RgkKX2ZvWp9x6+M0WojAcfmlPP4Hkq3dJ9nW71+xVwDP4JLUCPVZLy+fAvDVwH/0RUoXl4IH8+M6UVYduTJmmcb1qV2bQDXQ7MNihG8MEurGu8Rk32WNItpjb
EdcK0eRoT5KjxpYo/"
	}
```
