---
title: "How to sort IP addresses from the command line"
date: 2020-08-12T11:59:49-05:00
toc: false
images:
categories:
  - tech
tags: 
  - sort
  - ipv4
---

By breaking a list of IPv4 addresses into individual octets, the unix `sort` command will display the output in numerical order.

```
cat filewithipaddresses.txt | sort -t . -k 1,1n -k 2,2n -k 3,3n -k 4,4n
```

- `-t .` = Separator identifier used to split the IP address into individual components.
- `-k1,1n` = Each section of the IP address is (n)umerically sorted by the (k)ey.


Given a text file with random IP address:

```
jemurray@mbp-2019:~ $ cat filewithipaddresses.txt | head
10.1.3.2
254.1.3.2
192.168.37.254
192.168.37.16
192.168.37.221
192.168.37.18
192.168.37.210
192.168.37.136
192.168.37.222
192.168.37.223
```


Sorted output:

```
jemurray@mbp-2019:~ $ cat filewithipaddresses.txt | sort -t . -k 1,1n -k 2,2n -k 3,3n -k 4,4n | head

10.1.3.2
192.168.37.1
192.168.37.2
192.168.37.6
192.168.37.7
192.168.37.10
192.168.37.14
192.168.37.16
192.168.37.17
```