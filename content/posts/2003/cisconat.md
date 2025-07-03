---
title: "NAT configuration on Cisco router"
date: 2003-01-25T19:33:43-06:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - nat
---

```
!
! Last configuration change at 14:43:17 CST Sat Jan 25 2003
!
version 11.3
service timestamps debug uptime
service timestamps log uptime
service password-encryption
!
ip host-routing
ip nat pool external-addr 66.88.33.189 66.88.33.189 netmask 255.255.255.248
ip nat inside source list 10 pool external-addr overload
no ip finger
ip name-server 65.106.1.196
clock timezone CST -6
!
!
process-max-time 200
!
interface Loopback0
 no ip address
!
interface Ethernet0
 description private interface
 ip address 192.168.99.1 255.255.255.0
 ip nat inside
!
interface Ethernet1
 description public interface
 ip address 66.88.33.190 255.255.255.248
 ip nat outside
!
interface Serial0
 no ip address
 no ip mroute-cache
 shutdown
!
ip default-gateway 66.88.33.185
ip classless
!
logging buffered 4096 debugging
access-list 1 deny   any log
access-list 2 permit any
access-list 10 permit 192.168.99.0 0.0.0.255
access-list 11 permit 192.168.99.0 0.0.0.255
access-list 100 deny   udp any any eq 1434 log
access-list 100 permit ip any any
!
line con 0
line vty 0 4
 access-class 11 in
 login
!
ntp clock-period 17041922
ntp server 206.173.119.88
end
```