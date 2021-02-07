---
title: "Detect open network ports with nmap"
date: 2020-12-30T16:14:25-06:00
toc: false
images:
categories:
  - tech
tags: 
  - nmap
  - networking
  - security
---

# Overview

A fundamental best practice within network security is to limit the number of listening services exposed to the public Internet.  By limiting the attack surface of servers, rogue actors can not exploit what can't be seen.  

In the early 2000's it was common for a newly installed server to have `telnet`, `sendmail`, `ntp`, `apache`, etc running.  An unpatched flaw in any of these applications, is an avenue for a remote attacker to gain access to potentially sensitive data.  In recent years, system security posture has improved.  The default installation for most modern operating systems either protects the system with host based firewalls or disables services altogether.  It is still a good practice to validate proper protections are in place.   

There are numerous tools to display or scan for listening services.  My preference is `nmap`, the network port mapper.  It is a free and open source application that runs on Linux, macOS, and Windows.  While a command like `netstat` or `lsof` will display listening ports, using `nmap` from a remote host allows us to determine if additional firewalls are protecting the host more thoroughly.

# Running a basic nmap scan

Run: `sudo nmap -A <ip address>` to perform a basic scan with `nmap`.  The `-A` option enables a more aggressive scan with operating system detection, version detection, script scanning, and a traceroute at the end.   

Example output after scanning a Raspberry PI on my home network:

```
jemurray@kali:~$ sudo nmap -A 192.168.86.6
Starting Nmap 7.80 ( https://nmap.org ) at 2020-12-30 16:54 CST
Nmap scan report for raspberrypi.lan (192.168.86.6)
Host is up (0.0041s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.9p1 Raspbian 10+deb10u2 (protocol 2.0)
| ssh-hostkey:
|   2048 aa:7f:d0:bb:b8:e3:82:53:21:99:66:72:6f:ff:5a:07 (RSA)
|   256 c9:85:2f:d8:f4:5f:db:3a:4e:61:cb:34:df:17:8a:98 (ECDSA)
|_  256 27:d4:5c:eb:cc:51:64:89:bc:5e:15:ce:78:e3:38:de (ED25519)
MAC Address: B8:27:EB:05:38:6E (Raspberry Pi Foundation)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.80%E=4%D=12/30%OT=22%CT=1%CU=33995%PV=Y%DS=1%DC=D%G=Y%M=B827EB%
OS:TM=5FED0518%P=x86_64-pc-linux-gnu)SEQ(SP=10A%GCD=1%ISR=10B%TI=Z%CI=Z%II=
OS:I%TS=A)OPS(O1=M5B4ST11NW6%O2=M5B4ST11NW6%O3=M5B4NNT11NW6%O4=M5B4ST11NW6%
OS:O5=M5B4ST11NW6%O6=M5B4ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W
OS:6=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M5B4NNSNW6%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=
OS:O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD
OS:=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0
OS:%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1
OS:(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI
OS:=N%T=40%CD=S)
```



# Validating a laptop has no listening ports

I recommend running a tool like `nmap` against any portable devices such as laptops.  If these devices connect to `public wifi` it is important to protect them by disabling services or turning on a local firewall.   

On my macOS laptop, the firewall is enabled:

![](/images/2020-12-30-16-42-33.png)

`nmap` confirms there are no ports listening:

```
jemurray@kali:~$ sudo nmap -A 192.168.86.210
Starting Nmap 7.80 ( https://nmap.org ) at 2020-12-30 16:39 CST
Nmap scan report for mbp-2019.lan (192.168.86.210)
Host is up (0.022s latency).
All 1000 scanned ports on mbp-2019.lan (192.168.86.210) are closed
MAC Address: F0:18:98:9B:B5:B2 (Apple)
Too many fingerprints match this host to give specific OS details
Network Distance: 1 hop
```


# Comparison of local listening ports vs. remote listening ports

As we discussed previously, the `lsof` command will display all listening ports.  However, this may not represent services listening on the public network interface.  

For example, on this host there is a SMTP server listening:

```
jemurray@shell:~$ sudo lsof -n | grep LIST
exim4      3533                 Debian-exim    3u     IPv4           21882742      0t0        TCP *:smtp (LISTEN)
sshd      15426                        root    3u     IPv4           12655697      0t0        TCP *:ssh (LISTEN)
sshd      15426                        root    4u     IPv6           12655708      0t0        TCP *:ssh (LISTEN)
nginx     30170                        root    6u     IPv4           12851776      0t0        TCP *:http (LISTEN)
nginx     30170                        root    7u     IPv6           12851777      0t0        TCP *:http (LISTEN)
nginx     30170                        root    8u     IPv4           12851778      0t0        TCP *:https (LISTEN)
nginx     30170                        root    9u     IPv6           12851779      0t0        TCP *:https (LISTEN)
nginx     30174                    www-data    6u     IPv4           12851776      0t0        TCP *:http (LISTEN)
nginx     30174                    www-data    7u     IPv6           12851777      0t0        TCP *:http (LISTEN)
nginx     30174                    www-data    8u     IPv4           12851778      0t0        TCP *:https (LISTEN)
nginx     30174                    www-data    9u     IPv6           12851779      0t0        TCP *:https (LISTEN)
```

But a scan with `nmap` confirms `smtp` is not available to the public network:

```
jemurray@kali:~$ sudo nmap -A shell.jasonmurray.org
Starting Nmap 7.80 ( https://nmap.org ) at 2020-12-30 16:12 CST
Nmap scan report for shell.jasonmurray.org (104.131.191.87)
Host is up (0.027s latency).
Other addresses for shell.jasonmurray.org (not scanned): 2604:a880:800:10::19d5:4001
Not shown: 992 closed ports
PORT    STATE    SERVICE      VERSION
22/tcp  open     ssh          OpenSSH 7.9p1 Debian 10+deb10u2 (protocol 2.0)
| ssh-hostkey:
|   2048 19:5a:a9:c0:3f:05:0a:0b:4d:d7:a3:b8:32:34:f4:c5 (RSA)
|   256 6f:7c:4e:23:a5:d9:f2:c5:86:5f:e6:22:64:4e:29:64 (ECDSA)
|_  256 10:96:13:0c:03:24:43:9c:0f:8c:48:49:b7:1c:37:e9 (ED25519)
25/tcp  filtered smtp
80/tcp  open     http         nginx 1.14.2
|_http-server-header: nginx/1.14.2
|_http-title: 403 Forbidden
111/tcp filtered rpcbind
135/tcp filtered msrpc
139/tcp filtered netbios-ssn
443/tcp open     ssl/http     nginx 1.14.2
|_http-server-header: nginx/1.14.2
|_http-title: 400 No required SSL certificate was sent
| ssl-cert: Subject: commonName=CloudFlare Origin Certificate/organizationName=CloudFlare, Inc.
| Subject Alternative Name: DNS:*.jasonmurray.org, DNS:jasonmurray.org
| Not valid before: 2020-02-11T03:19:00
|_Not valid after:  2035-02-07T03:19:00
|_ssl-date: TLS randomness does not represent time
| tls-alpn:
|_  http/1.1
| tls-nextprotoneg:
|_  http/1.1
445/tcp filtered microsoft-ds
Aggressive OS guesses: Linux 3.10 - 4.11 (98%), Linux 3.2 - 4.9 (97%), Linux 2.6.32 - 3.13 (96%), Linux 3.16 - 4.6 (96%), Linux 4.10 (95%), Linux 4.4 (95%), Linux 2.6.22 - 2.6.36 (95%), Linux 2.6.32 (94%), Linux 3.10 (94%), Linux 2.6.32 - 3.10 (93%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 15 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```