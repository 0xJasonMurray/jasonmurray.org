---
title: "Basic Cisco and Juniper router configurations for remote management"
date: 2020-06-12T07:15:58-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - juniper
  - ssh
  - lab
---

This is the minimum configuration necessary to configure Cisco and Juniper routers for remote management via SSH.  I use the following base template configurations to enable IP via DHCP and SSH remote access for the lab environment.  


Cisco pastable:

```
hostname csr100v
ip domain-name example.com
aaa new-model
aaa authentication login default local
aaa authorization exec default local
crypto key generate rsa modulus 2048
username jemurray privilege 15 password MyPassword
interface GigabitEthernet0/1
 ip address dhcp
 no shut
ip ssh version 2
line vty 0 4
 privilege level 15
 transport input ssh
line vty 5 15
 privilege level 15
 transport input ssh
```

Juniper pastable:

```
set system host-name juniper-mx
set system root-authentication plain-text-password
set system login user jemurray authentication plain-text-password
set interfaces ge-0/0/7 unit 0 family inet dhcp
set system services ssh
```

