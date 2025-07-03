---
title: "Using lsof to determine which processes are listening on TCP or UDP ports"
date: 2020-12-27T19:14:24-06:00
toc: false
images:
categories:
  - tech
tags: 
  - lsof
  - linux
---

# TL;DR

Use `lsof` to find all processes listening on `TCP` or `UPD` ports:

```
sudo lsof -n | egrep 'TCP.*LISTEN|UDP'
```

# Details

After a routine security audit using `nmap`, a production server is found to be running a rogue server listening on port 8000:

```
jemurray@mbp-2019:~ $ nmap shell.jasonmurray.org
Starting Nmap 7.91 ( https://nmap.org ) at 2020-12-27 19:23 CST
Nmap scan report for shell.jasonmurray.org (104.131.191.87)
Host is up (0.067s latency).
Other addresses for shell.jasonmurray.org (not scanned): 2604:a880:800:10::19d5:4001
Not shown: 991 closed ports
PORT     STATE    SERVICE
22/tcp   open     ssh
80/tcp   open     http
443/tcp  open     https
8000/tcp open     http-alt
```

Connecting to port 8000 confirms the host is leaking files:

```
jemurray@mbp-2019:~ $ elinks shell.jasonmurray.org:8000 -dump
                            Directory listing for /

   --------------------------------------------------------------------------

     * [1]dontleakthisfile.txt

   --------------------------------------------------------------------------

References

   Visible links
   1. http://shell.jasonmurray.org:8000/dontleakthisfile.txt
```


Use `lsof` to find the process listening on port 8000.  In this example, we dump all process listening on `tcp` or `udp` ports:


```
jemurray@shell:~$ sudo lsof -n | egrep 'TCP.*LISTEN|UDP'
exim4      3533                 Debian-exim    3u     IPv4           21882742      0t0        TCP 127.0.0.1:smtp (LISTEN)
exim4      3533                 Debian-exim    4u     IPv6           21882743      0t0        TCP [::1]:smtp (LISTEN)
ntpd       3744                         ntp   18u     IPv4           55660414      0t0        UDP 127.0.0.1:ntp
ntpd       3744                         ntp   19u     IPv4           55660416      0t0        UDP 104.131.191.87:ntp
ntpd       3744                         ntp   20u     IPv4           55660418      0t0        UDP 10.17.0.5:ntp
ntpd       3744                         ntp   21u     IPv6           55660420      0t0        UDP [::1]:ntp
ntpd       3744  3747 ntpd              ntp   16u     IPv6           55660407      0t0        UDP *:ntp
ntpd       3744  3747 ntpd              ntp   17u     IPv4           55660410      0t0        UDP *:ntp
ntpd       3744  3747 ntpd              ntp   18u     IPv4           55660414      0t0        UDP 127.0.0.1:ntp
ntpd       3744  3747 ntpd              ntp   19u     IPv4           55660416      0t0        UDP 104.131.191.87:ntp
ntpd       3744  3747 ntpd              ntp   20u     IPv4           55660418      0t0        UDP 10.17.0.5:ntp
sshd      15426                        root    3u     IPv4           12655697      0t0        TCP *:ssh (LISTEN)
sshd      15426                        root    4u     IPv6           12655708      0t0        TCP *:ssh (LISTEN)
python3   16760                    jemurray    3u     IPv4           57858801      0t0        TCP *:8000 (LISTEN)
nginx     30170                        root    6u     IPv4           12851776      0t0        TCP *:http (LISTEN)
nginx     30170                        root    7u     IPv6           12851777      0t0        TCP *:http (LISTEN)
nginx     30170                        root    8u     IPv4           12851778      0t0        TCP *:https (LISTEN)
nginx     30170                        root    9u     IPv6           12851779      0t0        TCP *:https (LISTEN)
nginx     30174                    www-data    6u     IPv4           12851776      0t0        TCP *:http (LISTEN)
nginx     30174                    www-data    7u     IPv6           12851777      0t0        TCP *:http (LISTEN)
nginx     30174                    www-data    8u     IPv4           12851778      0t0        TCP *:https (LISTEN)
nginx     30174                    www-data    9u     IPv6           12851779      0t0        TCP *:https (LISTEN)
```

Looking at the `python3` processes, we see:

```
jemurray@shell:~$ ps -ef | grep python3
jemurray 16760 13824  0 01:19 pts/8    00:00:00 python3 -m http.server 8000
```

`lsof` will also display which directory the `python3` program is sharing:

```
jemurray@shell:~$ sudo lsof | grep 16760 | grep cwd
python3   16760                    jemurray  cwd       DIR              254,1     4096     264931 /home/jemurray/example-hacked-directory
```

