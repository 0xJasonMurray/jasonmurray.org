---
title: "Configuring RPKI on Juniper vMX with the Fort validation server"
date: 2020-10-11T06:15:18-05:00
toc: false
images:
categories:
  - tech
tags: 
  - rpki
  - fort
---

# Overview

In the simplest terms, RPKI helps to solve the problem of trust within routing exchange points.  As Internet routers exchange routes through BGP, there is an inherent level of trust that each member will only announce networks they have the authority over.   In practice, this works pretty well.  There have been a few high profile network hijacking incidents in recent years, but good customer validation and filtering practices limit these occurrences.  Diligent use of LoA's and IRR databases help to ensure network operators or people with malicious intent are kept at bay.  RPKI enhances these tools by allowing network owners to cryptographically sign individual network routes.  

When network providers enable RPKI validation, each route learned through BGP is compared to the RPKI database.   If the origin is valid, the route is placed in the routing table.   If the route does not pass RPKI validation, it may be rejected.  How network operators handle RPKI validation status is up to them.  For providers interested in learning more about RPKI it can be enabled in a "monitoring mode" only.   It allows us to gain experience with the RPKI protocol without accidentally black-holing large portions of the Internet.

Cloudflare [wrote a fantastic overview of RPKI](https://blog.cloudflare.com/rpki/).  I would highly recommend heading over there first.  The goal of this post is to detail the basics of [setting up and enforcing RPKI on a Juniper router](https://www.juniper.net/documentation/en_US/junos/topics/topic-map/bgp-origin-as-validation.html) with the [FORT validation server](https://fortproject.net/en/validator).


The following network topology will be used throughout this post:

![juniper rpki topology](/images/juniperrpki.png)


# Details

## ExaBGP

To make this example useful, we need real-world routes injected into our routing table.   ExaBGP + RIPE routing snapshots allows us to pull a full set of routes from the current Internet announcements.

Setting up the ExaBGP server is similar to these instructions: [Simulating the full Internet routing table with ExaBGP in GNS3](/posts/2020/exabgp-fulltable/)

In this project, I deviated from the above instructions by:

- Using a physical Ubuntu 18.04 server instead of the GNS3 VM.  I wanted a faster system that was better connected to the Internet.
- Removed all but 30k routes from the RIPE routing data.  Carrying the full routing table is unnecessary to test the RPKI features and functionality.



## RPKI Validation Server Configuration

There are a number of RPKI validation servers to choose from.   My goal was to find a system that was easy to setup and does not require too many external dependencies, such as Java.   The Fort server compiled easily on a stock Ubuntu server.

System overview:

- Ubuntu 18.04
- Fort v1.4.1

Install the prerequisite software:

```
sudo apt install autoconf automake build-essential libjansson-dev libssl-dev pkg-config rsync libssl-dev libjansson-dev pkg-config libcurl4-openssl-dev libxml2-dev 
```

Download and install Fort:

```
wget https://github.com/NICMx/FORT-validator/releases/download/v1.4.1/fort-1.4.1.tar.gz
tar zxvf fort-1.4.1.tar.gz
fort-1.4.1/
./configure
make
sudo make install
```

Configure Fort with the recommended `fort_setup.sh` script:

```
wget wget https://raw.githubusercontent.com/NICMx/FORT-validator/v1.4.1/fort_setup.sh
chmod 755 ./fort_setup.sh
mkdir tal
./fort_setup.sh ./tal
...
------------------------------------------------------
--------------------   Success!   --------------------
------------------------------------------------------

- The five RIRs TAL's were downloaded to '/home/jemurray/tal'.
- The directory /tmp/fort/repository was created, so it can be used as the local repository.
- The configuration file '/home/jemurray/fort-config.json' was created with the following content:
{
  "local-repository": "/tmp/fort/repository",
  "tal": "/home/jemurray/tal"
}

- This configuration file can be utilized with FORT validator, e.g.:
  $ fort -f "/home/jemurray/fort-config.json"
- Or its members can be utilized as FORT validator arguments, e.g.:
  $ fort --tal "/home/jemurray/tal" --local-repository "/tmp/fort/repository"
```


Start the Fort software.  To avoid running the Fort server as `root`, the port is changed from the default to `8323`.  The IP address the server should listen on is changed from default to `192.168.86.5`:

```
fort -f ./fort-config.json --server.address="192.168.86.5#8323" --log.level=debug
```

## Juniper Router Configuration


Configure the Juniper vMX to peer with the Fort server:

```
set routing-options validation group rpki-validation session 192.168.86.5 port 8323
```


Confirm the Juniper router is connected to the RPKI validation server: 

```
jemurray@juniper-2> show validation session
Session                                  State   Flaps     Uptime #IPv4/IPv6 records
192.168.86.5                             Ex-Full     1   00:01:38 157921/26671
```

Configure a routing policy that:

- Accepts RPKI validated routes
- Accepts RPKI unknown routes
- Rejects RPKI invalid routes

**IMPORTANT NOTE:** If the goal is to gain RPKI operational experience without accidentally black-holing routes or causing unnecessary network outages.   Don't `reject` `invalid` routes in the configuration example below.   Instead, `accept` all routes.  This way you can monitor the routing table received from neighbors for any issues.   Once everything looks good, then flip over to `rejecting` invalid routes.  Later in the example, we will show how to check the route validation status.

```
set policy-options policy-statement validation term valid from protocol bgp
set policy-options policy-statement validation term valid from validation-database valid
set policy-options policy-statement validation term valid then validation-state valid
set policy-options policy-statement validation term valid then accept
set policy-options policy-statement validation term invalid from protocol bgp
set policy-options policy-statement validation term invalid from validation-database invalid
set policy-options policy-statement validation term invalid then validation-state invalid
set policy-options policy-statement validation term invalid then reject
set policy-options policy-statement validation term unknown from protocol bgp
set policy-options policy-statement validation term unknown then validation-state unknown
set policy-options policy-statement validation term unknown then accept
```

Show `valid` routes;

```
jemurray@juniper-2> show route validation-state valid

inet.0: 23349 destinations, 23349 routes (18397 active, 0 holddown, 4952 hidden)
+ = Active Route, - = Last Active, * = Both

202.79.16.0/21     *[BGP/170] 01:31:25, MED 110, localpref 100
                      AS path: 52320 9498 17494 24481 24481 24481 I, validation-state: valid
                    >  to 192.168.86.5 via ge-0/0/1.0
202.79.24.0/24     *[BGP/170] 01:31:25, MED 0, localpref 100
                      AS path: 52320 2914 24492 ?, validation-state: valid
                    >  to 192.168.86.5 via ge-0/0/1.0
```


Show `invalid` routes.  These routes are rejected from the routing table and hidden:

```
jemurray@juniper-2> show route validation-state invalid hidden

inet.0: 23349 destinations, 23349 routes (18397 active, 0 holddown, 4952 hidden)
+ = Active Route, - = Last Active, * = Both

202.81.164.0/23     [BGP ] 01:30:18, MED 0, localpref 100
                      AS path: 52320 9304 55821 17970 17970 17970 17970 17970 24306 I, validation-state: invalid
                    >  to 192.168.86.5 via ge-0/0/1.0
202.81.166.0/24     [BGP ] 01:30:18, MED 0, localpref 100
                      AS path: 52320 9304 55821 4779 23944 17970 24306 I, validation-state: invalid
                    >  to 192.168.86.5 via ge-0/0/1.0
```


# Appendix

## Full router configuration:

```
jemurray@juniper-2> show configuration | display set | except encrypted-password
set version 20190829.221548_builder.r1052644
set system host-name juniper-2
set system login user jemurray uid 2000
set system login user jemurray class super-user
set system services ssh
set system syslog user * any emergency
set system syslog file messages any notice
set system syslog file messages authorization info
set system syslog file interactive-commands interactive-commands any
set system processes dhcp-service traceoptions file dhcp_logfile
set system processes dhcp-service traceoptions file size 10m
set system processes dhcp-service traceoptions level all
set system processes dhcp-service traceoptions flag packet
set interfaces ge-0/0/1 unit 0 family inet dhcp
set interfaces fxp0 unit 0 family inet dhcp vendor-id Juniper-vmx-VM5F52E443C7
set policy-options policy-statement exabgp-import term next-hop from protocol bgp
set policy-options policy-statement exabgp-import term next-hop then next-hop 192.168.86.5
set policy-options policy-statement validation term valid from protocol bgp
set policy-options policy-statement validation term valid from validation-database valid
set policy-options policy-statement validation term valid then validation-state valid
set policy-options policy-statement validation term valid then community add origin-validation-state-valid
set policy-options policy-statement validation term valid then accept
set policy-options policy-statement validation term invalid from protocol bgp
set policy-options policy-statement validation term invalid from validation-database invalid
set policy-options policy-statement validation term invalid then validation-state invalid
set policy-options policy-statement validation term invalid then community add origin-validation-state-invalid
set policy-options policy-statement validation term invalid then reject
set policy-options policy-statement validation term unknown from protocol bgp
set policy-options policy-statement validation term unknown then validation-state unknown
set policy-options policy-statement validation term unknown then community add origin-validation-state-unknown
set policy-options policy-statement validation term unknown then accept
set policy-options community origin-validation-state-invalid members 0x4300:0.0.0.0:2
set policy-options community origin-validation-state-unknown members 0x4300:0.0.0.0:1
set policy-options community origin-validation-state-valid members 0x4300:0.0.0.0:0
set routing-options validation group rpki-validation session 192.168.86.5 port 8323
set routing-options autonomous-system 64514
set protocols bgp group exabgp type external
set protocols bgp group exabgp import validation
set protocols bgp group exabgp neighbor 192.168.86.5 import exabgp-import
deactivate protocols bgp group exabgp neighbor 192.168.86.5 import
set protocols bgp group exabgp neighbor 192.168.86.5 peer-as 64515
```

## Full ExaBGP Config

```
jemurray@home-server:~$ cat exabgp.cfg
template {
  neighbor juniper-2 {
    local-as 64515;
    family {
      ipv4 unicast;
      ipv6 unicast;
    }
  }
}

neighbor 192.168.86.32 {
  inherit juniper-2;
  router-id 192.168.86.5;
  local-address 192.168.86.5;
  peer-as 64514;
}

process fullbgp {
  run /usr/bin/python3 /home/jemurray/partialbgptable.py;
  encoder text;
}
```

