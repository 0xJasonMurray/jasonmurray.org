---
title: "Linux IPtables rules"
date: 2003-07-23T19:35:43-06:00
toc: false
images:
categories:
  - tech
tags: 
  - iptables
---

Used in /etc/network/if-up.d/S01tables

```
#!/bin/bash

modprobe ip_tables
modprobe iptable_filter
modprobe iptable_mangle
modprobe iptable_nat
modprobe ipt_state
modprobe ipt_REJECT
modprobe ipt_LOG

iptables --flush

iptables -P INPUT DROP

iptables -A INPUT -i lo -j ACCEPT
iptables -A INPUT -p tcp -m tcp --tcp-flags ACK ACK -j ACCEPT
iptables -A INPUT -m state --state ESTABLISHED -j ACCEPT
iptables -A INPUT -m state --state RELATED -j ACCEPT
iptables -A INPUT -p icmp -m icmp --icmp-type echo-reply -j ACCEPT
iptables -A INPUT -p icmp -m icmp --icmp-type destination-unreachable -j ACCEPT
iptables -A INPUT -p icmp -m icmp --icmp-type source-quench -j ACCEPT
iptables -A INPUT -p icmp -m icmp --icmp-type time-exceeded -j ACCEPT
iptables -A INPUT -p icmp -m icmp --icmp-type parameter-problem -j ACCEPT
iptables -A INPUT -p tcp -m tcp --source 192.168.99.0/24 --dport 20 -j ACCEPT
iptables -A INPUT -p tcp -m tcp --source 192.168.99.0/24 --dport 21 -j ACCEPT
iptables -A INPUT -p tcp -m tcp --source 192.168.99.0/24 --dport 22 -j ACCEPT
iptables -A INPUT -p tcp -m tcp --source 192.168.99.0/24 --dport 80 -j ACCEPT
iptables -A INPUT -p udp -m udp --source 192.168.99.0/24 --dport 69 -j ACCEPT
iptables -A INPUT -p tcp -m tcp --source 192.168.99.0/24 --dport 179 -j ACCEPT
iptables -A INPUT -j LOG
iptables -A INPUT -j REJECT
```