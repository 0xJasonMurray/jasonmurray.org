---
title: "Enable firewalling services with UFW on Ubuntu"
date: 2020-10-14T14:12:18-05:00
toc: false
images:
categories:
  - tech
tags: 
  - iptables
  - ubuntu
  - ufw
---

By default the firewall is disabled on an Ubuntu server.  We can verify this by looking at the `iptables` rules.  In this example, the default policy is set to `ACCEPT`:

```
jemurray@home-server:~$ sudo iptables --list -nv
Chain INPUT (policy ACCEPT 7523K packets, 10G bytes)
 pkts bytes target     prot opt in     out     source               destination
   69  2887 ACCEPT     udp  --  ens160 *       0.0.0.0/0            0.0.0.0/0            udp dpt:1194
    0     0 ACCEPT     all  --  tun0   *       0.0.0.0/0            0.0.0.0/0

Chain FORWARD (policy ACCEPT 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  tun0   ens160  0.0.0.0/0            0.0.0.0/0
    0     0 ACCEPT     all  --  ens160 tun0    0.0.0.0/0            0.0.0.0/0
```

To keep firewall management simple, `ufw` is used to manage the firewall.  We can verify again, there is no firewall running by default:

```
jemurray@home-server:~$ sudo ufw status
Status: inactive
```

Before changing the default policy to `deny`, make sure to allow remote management.  On my system, SSH is allowed from the world.  A more restrictive rule can be enabled if necessary:

```
jemurray@home-server:~$ sudo ufw allow ssh
Rules updated
Rules updated (v6)
```

Change the default policy from `allow` to `deny`:

```
jemurray@home-server:~$ sudo ufw default deny
Default incoming policy changed to 'deny'
(be sure to update your rules accordingly)
```

Enable the firewall:

```
jemurray@home-server:~$ sudo ufw enable
Firewall is active and enabled on system startup
```

Validate the firewall is enabled:

```
jemurray@home-server:~$ sudo ufw status verbose
Status: active
Logging: on (low)
Default: deny (incoming), allow (outgoing), deny (routed)
New profiles: skip

To                         Action      From
--                         ------      ----
22/tcp                     ALLOW IN    Anywhere
60001:61000/udp            ALLOW IN    Anywhere
22/tcp (v6)                ALLOW IN    Anywhere (v6)
60001:61000/udp (v6)       ALLOW IN    Anywhere (v6)
```

`ufw` creates a lot of iptables rules:

```
jemurray@home-server:~$ sudo iptables --list -nv
[sudo] password for jemurray:
Chain INPUT (policy DROP 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
   69  2887 ACCEPT     udp  --  ens160 *       0.0.0.0/0            0.0.0.0/0            udp dpt:1194
    0     0 ACCEPT     all  --  tun0   *       0.0.0.0/0            0.0.0.0/0
 2568 1736K ufw-before-logging-input  all  --  *      *       0.0.0.0/0            0.0.0.0/0
 2568 1736K ufw-before-input  all  --  *      *       0.0.0.0/0            0.0.0.0/0
   25  6569 ufw-after-input  all  --  *      *       0.0.0.0/0            0.0.0.0/0
    2   178 ufw-after-logging-input  all  --  *      *       0.0.0.0/0            0.0.0.0/0
    2   178 ufw-reject-input  all  --  *      *       0.0.0.0/0            0.0.0.0/0
    2   178 ufw-track-input  all  --  *      *       0.0.0.0/0            0.0.0.0/0

Chain FORWARD (policy DROP 0 packets, 0 bytes)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  tun0   ens160  0.0.0.0/0            0.0.0.0/0
    0     0 ACCEPT     all  --  ens160 tun0    0.0.0.0/0            0.0.0.0/0
    0     0 ufw-before-logging-forward  all  --  *      *       0.0.0.0/0            0.0.0.0/0
    0     0 ufw-before-forward  all  --  *      *       0.0.0.0/0            0.0.0.0/0
    0     0 ufw-after-forward  all  --  *      *       0.0.0.0/0            0.0.0.0/0
    0     0 ufw-after-logging-forward  all  --  *      *       0.0.0.0/0            0.0.0.0/0
    0     0 ufw-reject-forward  all  --  *      *       0.0.0.0/0            0.0.0.0/0
    0     0 ufw-track-forward  all  --  *      *       0.0.0.0/0            0.0.0.0/0

Chain OUTPUT (policy ACCEPT 75 packets, 6300 bytes)
 pkts bytes target     prot opt in     out     source               destination
 2633 1773K ufw-before-logging-output  all  --  *      *       0.0.0.0/0            0.0.0.0/0
 2633 1773K ufw-before-output  all  --  *      *       0.0.0.0/0            0.0.0.0/0
  135 10596 ufw-after-output  all  --  *      *       0.0.0.0/0            0.0.0.0/0
  135 10596 ufw-after-logging-output  all  --  *      *       0.0.0.0/0            0.0.0.0/0
  135 10596 ufw-reject-output  all  --  *      *       0.0.0.0/0            0.0.0.0/0
  135 10596 ufw-track-output  all  --  *      *       0.0.0.0/0            0.0.0.0/0

Chain ufw-after-forward (1 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-after-input (1 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ufw-skip-to-policy-input  udp  --  *      *       0.0.0.0/0            0.0.0.0/0            udp dpt:137
    1   229 ufw-skip-to-policy-input  udp  --  *      *       0.0.0.0/0            0.0.0.0/0            udp dpt:138
    0     0 ufw-skip-to-policy-input  tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:139
    0     0 ufw-skip-to-policy-input  tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:445
   22  6162 ufw-skip-to-policy-input  udp  --  *      *       0.0.0.0/0            0.0.0.0/0            udp dpt:67
    0     0 ufw-skip-to-policy-input  udp  --  *      *       0.0.0.0/0            0.0.0.0/0            udp dpt:68
    0     0 ufw-skip-to-policy-input  all  --  *      *       0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type BROADCAST

Chain ufw-after-logging-forward (1 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 LOG        all  --  *      *       0.0.0.0/0            0.0.0.0/0            limit: avg 3/min burst 10 LOG flags 0 level 4 prefix "[UFW BLOCK] "

Chain ufw-after-logging-input (1 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 LOG        all  --  *      *       0.0.0.0/0            0.0.0.0/0            limit: avg 3/min burst 10 LOG flags 0 level 4 prefix "[UFW BLOCK] "

Chain ufw-after-logging-output (1 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-after-output (1 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-before-forward (1 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED
    0     0 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0            icmptype 3
    0     0 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0            icmptype 11
    0     0 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0            icmptype 12
    0     0 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0            icmptype 8
    0     0 ufw-user-forward  all  --  *      *       0.0.0.0/0            0.0.0.0/0

Chain ufw-before-input (1 references)
 pkts bytes target     prot opt in     out     source               destination
 1323 1601K ACCEPT     all  --  lo     *       0.0.0.0/0            0.0.0.0/0
 1211  128K ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED
    0     0 ufw-logging-deny  all  --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate INVALID
    0     0 DROP       all  --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate INVALID
    0     0 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0            icmptype 3
    0     0 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0            icmptype 11
    0     0 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0            icmptype 12
    0     0 ACCEPT     icmp --  *      *       0.0.0.0/0            0.0.0.0/0            icmptype 8
    2   656 ACCEPT     udp  --  *      *       0.0.0.0/0            0.0.0.0/0            udp spt:67 dpt:68
   30  6803 ufw-not-local  all  --  *      *       0.0.0.0/0            0.0.0.0/0
    0     0 ACCEPT     udp  --  *      *       0.0.0.0/0            224.0.0.251          udp dpt:5353
    0     0 ACCEPT     udp  --  *      *       0.0.0.0/0            239.255.255.250      udp dpt:1900
   30  6803 ufw-user-input  all  --  *      *       0.0.0.0/0            0.0.0.0/0

Chain ufw-before-logging-forward (1 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-before-logging-input (1 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-before-logging-output (1 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-before-output (1 references)
 pkts bytes target     prot opt in     out     source               destination
 1323 1601K ACCEPT     all  --  *      lo      0.0.0.0/0            0.0.0.0/0
 1175  162K ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate RELATED,ESTABLISHED
  133 10418 ufw-user-output  all  --  *      *       0.0.0.0/0            0.0.0.0/0

Chain ufw-logging-allow (0 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 LOG        all  --  *      *       0.0.0.0/0            0.0.0.0/0            limit: avg 3/min burst 10 LOG flags 0 level 4 prefix "[UFW ALLOW] "

Chain ufw-logging-deny (2 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 RETURN     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate INVALID limit: avg 3/min burst 10
    0     0 LOG        all  --  *      *       0.0.0.0/0            0.0.0.0/0            limit: avg 3/min burst 10 LOG flags 0 level 4 prefix "[UFW BLOCK] "

Chain ufw-not-local (1 references)
 pkts bytes target     prot opt in     out     source               destination
    7   412 RETURN     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type LOCAL
    0     0 RETURN     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type MULTICAST
   23  6391 RETURN     all  --  *      *       0.0.0.0/0            0.0.0.0/0            ADDRTYPE match dst-type BROADCAST
    0     0 ufw-logging-deny  all  --  *      *       0.0.0.0/0            0.0.0.0/0            limit: avg 3/min burst 10
    0     0 DROP       all  --  *      *       0.0.0.0/0            0.0.0.0/0

Chain ufw-reject-forward (1 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-reject-input (1 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-reject-output (1 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-skip-to-policy-forward (0 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 DROP       all  --  *      *       0.0.0.0/0            0.0.0.0/0

Chain ufw-skip-to-policy-input (7 references)
 pkts bytes target     prot opt in     out     source               destination
   23  6391 DROP       all  --  *      *       0.0.0.0/0            0.0.0.0/0

Chain ufw-skip-to-policy-output (0 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0

Chain ufw-track-forward (1 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-track-input (1 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-track-output (1 references)
 pkts bytes target     prot opt in     out     source               destination
   24  1440 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate NEW
   34  2678 ACCEPT     udp  --  *      *       0.0.0.0/0            0.0.0.0/0            ctstate NEW

Chain ufw-user-forward (1 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-user-input (1 references)
 pkts bytes target     prot opt in     out     source               destination
    7   412 ACCEPT     tcp  --  *      *       0.0.0.0/0            0.0.0.0/0            tcp dpt:22

Chain ufw-user-limit (0 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 LOG        all  --  *      *       0.0.0.0/0            0.0.0.0/0            limit: avg 3/min burst 5 LOG flags 0 level 4 prefix "[UFW LIMIT BLOCK] "
    0     0 REJECT     all  --  *      *       0.0.0.0/0            0.0.0.0/0            reject-with icmp-port-unreachable

Chain ufw-user-limit-accept (0 references)
 pkts bytes target     prot opt in     out     source               destination
    0     0 ACCEPT     all  --  *      *       0.0.0.0/0            0.0.0.0/0

Chain ufw-user-logging-forward (0 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-user-logging-input (0 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-user-logging-output (0 references)
 pkts bytes target     prot opt in     out     source               destination

Chain ufw-user-output (1 references)
 pkts bytes target     prot opt in     out     source               destination
```

