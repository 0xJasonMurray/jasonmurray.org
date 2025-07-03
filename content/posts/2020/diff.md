---
title: "Quickly find changes in network configurations files with diff"
date: 2020-06-18T16:52:20-05:00
toc: false
images:
categories:
  - tech
tags: 
  - diff
  - configurationchanges
---

The `diff` utility is excellent for quickly spotting changes in network configuration files.  After all major network changes, upgrades, or maintenance this is one of my staple commands to validate there are no unexpected changes.   It is especially helpful for router configurations that are thousands of lines long.

Start with a quick overview of what changed, by running `diff` with no options:

```
jemurray@mbp-2019:~ $ diff previous-config.txt current-config.txt
60c60
< username jemurray privilege 15 password 0 CiscoLab123
---
> username jemurray privilege 15 password 0 CiscoLab
87a88
>  description Uplink Interface
94d94
<  description shutdown
```

Find specific differences line-by-line in side-by-side mode, by running: `diff -W 120 -y previous-config.txt current-config.txt`

- -W: sets the width to 120 characters.  Adjust as necessary to fit the full configuration on a single screen.
- -y: side-by-side

Differences in the files are indicated by the following symbols:

- <: line is in the previous file, but not current.
- >: line is in the current file, but not previous.
- |: line is in both files, but changed.  

```
jemurray@mbp-2019:~ $ diff -W 120 -y previous-config.txt current-config.txt
csr100v#show run						csr100v#show run
Building configuration...					Building configuration...


Current configuration : 3382 bytes				Current configuration : 3382 bytes
!								!
! Last configuration change at 03:11:05 UTC Tue May 19 2	! Last configuration change at 03:11:05 UTC Tue May 19 2
!								!
version 15.7							version 15.7
service timestamps debug datetime msec				service timestamps debug datetime msec
service timestamps log datetime msec				service timestamps log datetime msec
no service password-encryption					no service password-encryption
!								!
hostname csr100v						hostname csr100v
!								!
boot-start-marker						boot-start-marker
boot-end-marker							boot-end-marker
!								!
!								!
!								!
aaa new-model							aaa new-model
!								!
!								!
aaa authentication login default local				aaa authentication login default local
!								!
!								!
!								!
!								!
!								!
aaa session-id common						aaa session-id common
!								!
!								!
!								!
mmi polling-interval 60						mmi polling-interval 60
no mmi auto-configure						no mmi auto-configure
no mmi pvc							no mmi pvc
mmi snmp-timeout 180						mmi snmp-timeout 180
!								!
!								!
!								!
!								!
!								!
no ip icmp rate-limit unreachable				no ip icmp rate-limit unreachable
!								!
!								!
!								!
!								!
!								!
!								!
no ip domain lookup						no ip domain lookup
ip domain name example.com					ip domain name example.com
ip cef								ip cef
no ipv6 cef							no ipv6 cef
!								!
multilink bundle-name authenticated				multilink bundle-name authenticated
!								!
!								!
!								!
!								!
username jemurray privilege 15 password 0 CiscoLab123	   |	username jemurray privilege 15 password 0 CiscoLab
!								!
redundancy							redundancy
!								!
no cdp log mismatch duplex					no cdp log mismatch duplex
!								!
ip tcp synwait-time 5						ip tcp synwait-time 5
!								!
!								!
!								!
!								!
!								!
!								!
!								!
!								!
!								!
!								!
!								!
!								!
!								!
interface GigabitEthernet0/0					interface GigabitEthernet0/0
 no ip address							 no ip address
 shutdown							 shutdown
 duplex auto							 duplex auto
 speed auto							 speed auto
 media-type rj45						 media-type rj45
!								!
interface GigabitEthernet0/1					interface GigabitEthernet0/1
							   >	 description Uplink Interface
 ip address dhcp						 ip address dhcp
 duplex auto							 duplex auto
 speed auto							 speed auto
 media-type rj45						 media-type rj45
!								!
interface GigabitEthernet0/2					interface GigabitEthernet0/2
 description shutdown					   <
 no ip address							 no ip address
 shutdown							 shutdown
 duplex auto							 duplex auto
 speed auto							 speed auto
 media-type rj45						 media-type rj45
!								!
interface GigabitEthernet0/3					interface GigabitEthernet0/3
 no ip address							 no ip address
 shutdown							 shutdown
 duplex auto							 duplex auto
 speed auto							 speed auto
 media-type rj45						 media-type rj45
!								!
ip forward-protocol nd						ip forward-protocol nd
!								!
!								!
no ip http server						no ip http server
no ip http secure-server					no ip http secure-server
ip ssh version 2						ip ssh version 2
!								!
ipv6 ioam timestamp						ipv6 ioam timestamp
!								!
!								!
!								!
!								!
!								!
control-plane							control-plane
!								!
banner exec ^C							banner exec ^C
********************************************************	********************************************************
* IOSv is strictly limited to use for evaluation, demons	* IOSv is strictly limited to use for evaluation, demons
* education. IOSv is provided as-is and is not supported	* education. IOSv is provided as-is and is not supported
* Technical Advisory Center. Any use or disclosure, in w	* Technical Advisory Center. Any use or disclosure, in w
* of the IOSv Software or Documentation to any third par	* of the IOSv Software or Documentation to any third par
* purposes is expressly prohibited except as otherwise a	* purposes is expressly prohibited except as otherwise a
* Cisco in writing.                                     	* Cisco in writing.
********************************************************	********************************************************
banner incoming ^C						banner incoming ^C
********************************************************	********************************************************
* IOSv is strictly limited to use for evaluation, demons	* IOSv is strictly limited to use for evaluation, demons
* education. IOSv is provided as-is and is not supported	* education. IOSv is provided as-is and is not supported
* Technical Advisory Center. Any use or disclosure, in w	* Technical Advisory Center. Any use or disclosure, in w
* of the IOSv Software or Documentation to any third par	* of the IOSv Software or Documentation to any third par
* purposes is expressly prohibited except as otherwise a	* purposes is expressly prohibited except as otherwise a
* Cisco in writing.                                     	* Cisco in writing.
********************************************************	********************************************************
banner login ^C							banner login ^C
********************************************************	********************************************************
* IOSv is strictly limited to use for evaluation, demons	* IOSv is strictly limited to use for evaluation, demons
* education. IOSv is provided as-is and is not supported	* education. IOSv is provided as-is and is not supported
* Technical Advisory Center. Any use or disclosure, in w	* Technical Advisory Center. Any use or disclosure, in w
* of the IOSv Software or Documentation to any third par	* of the IOSv Software or Documentation to any third par
* purposes is expressly prohibited except as otherwise a	* purposes is expressly prohibited except as otherwise a
* Cisco in writing.                                     	* Cisco in writing.
********************************************************	********************************************************
!								!
line con 0							line con 0
 exec-timeout 0 0						 exec-timeout 0 0
 privilege level 15						 privilege level 15
 logging synchronous						 logging synchronous
line aux 0							line aux 0
 exec-timeout 0 0						 exec-timeout 0 0
 privilege level 15						 privilege level 15
 logging synchronous						 logging synchronous
line vty 0 4							line vty 0 4
 privilege level 15						 privilege level 15
 transport input ssh						 transport input ssh
line vty 5 15							line vty 5 15
 privilege level 15						 privilege level 15
 transport input ssh						 transport input ssh
!								!
no scheduler allocate						no scheduler allocate
!								!
```

