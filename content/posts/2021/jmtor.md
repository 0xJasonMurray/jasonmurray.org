---
title: "Adding jasonmurray.org as a TOR hidden service"
date: 2021-01-15T12:53:14-06:00
toc: false
images:
categories:
  - tech
tags: 
  - tor
  - hiddenservice
---

# Overview

Installation instructions to create a TOR hidden service on a Debian 10 server.   In this example, I am adding the website `jasonmurray.org` and accompanying `ssh` server as a TOR hidden service.  TOR is typically used to "hide" the owners or location of a server.  In this setup, I am adding my server to TOR as an alternative way to access the resources and learn something new.  In other words, I am not trying to hide the server or my identity.

WARNING: Do not follow these instructions if strong anonymity is required.  I take no responsibility for misconfigured systems.

# Details

There is already a working `nginx` server listening on ports 80 and 443.   Configuration of the webserver is outside the scope of this document.  

On Debian 10, use `apt` to install `tor`.  According to the installation instructions on the [TOR website](https://2019.www.torproject.org/docs/debian.html.en) Debian uses the LTS TOR release.  Refer to the TOR installation instructions for other operating systems:

```
sudo apt install tor
```

Configure the hidden service by adding or uncommenting the following lines in the `/etc/tor/torrc` file.  In my example, I would like the `nginx` and `ssh` server listening on the TOR network:

```
HiddenServiceDir /var/lib/tor/hidden_service/
HiddenServicePort 80 127.0.0.1:80
HiddenServicePort 22 127.0.0.1:22
```

Restart `tor`:

```
jemurray@shell:/etc/tor$ sudo /etc/init.d/tor restart
[ ok ] Restarting tor (via systemctl): tor.service.
```

Retrieve the `tor` hidden node name:

```
jemurray@shell:/etc/tor$ sudo cat  /var/lib/tor/hidden_service/hostname
trspv4gsa5irkrflbskyzwfo6vsj5h6i6zaelgc52hxmuoz6w6xpzbid.onion
```

The onion address for the hidden services is:

```
trspv4gsa5irkrflbskyzwfo6vsj5h6i6zaelgc52hxmuoz6w6xpzbid.onion
```

Browse to the website using the onion address above:

![](/images/2021-01-15-13-20-14.png)


Test the `ssh` session through a TOR SOCKS proxy:

```
jemurray@jasons-mbp:~ $ ssh -o ProxyCommand='nc -x 127.0.0.1:9050 %h %p' trspv4gsa5irkrflbskyzwfo6vsj5h6i6zaelgc52hxmuoz6w6xpzbid.onion
The authenticity of host 'trspv4gsa5irkrflbskyzwfo6vsj5h6i6zaelgc52hxmuoz6w6xpzbid.onion (<no hostip for proxy command>)' can't be established.
ECDSA key fingerprint is SHA256:ruj7HumJB4qijyZaAAZw/XPVbsnYv2rWkNz0SL35n1A.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'trspv4gsa5irkrflbskyzwfo6vsj5h6i6zaelgc52hxmuoz6w6xpzbid.onion' (ECDSA) to the list of known hosts.
Autopushing login request to phone...
Success. Logging you in...
Linux shell 4.19.0-6-cloud-amd64 #1 SMP Debian 4.19.67-2+deb10u1 (2019-09-20) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
No mail.
jemurray@shell:~$
```

Finally, backup the `tor` hidden services `keys` in the following directory: `/var/lib/tor/hidden_service` - They are needed in order to use the same URL on another server or in the event of a drive failure.  