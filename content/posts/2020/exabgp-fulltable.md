---
title: "Simulating the full Internet routing table with ExaBGP in GNS3"
date: 2020-08-02T13:26:22-05:00
toc: false
images:
categories:
  - tech
tags: 
  - gns3
  - vmx
  - juniper
  - exabgp
  - ripe
  - mrtparse
---



A combination of GNS3, a handful of [Juniper vMXs](/posts/2020/vmxongns3/), and ExaBGP allows us to simulate the WashU edge and aggregation routers used in production.   In this post I will focus on how we use ExaBGP to simulate a real Internet routing table.

Network topology for one of the edge routers in GNS3:

![gns3 juniper edge](/images/edgejunipergns3.png)

The server running ExaBGP is an Ubuntu 18.04 Cloud Guest image from GNS3.  This system has 2 network interfaces.  The first one connects to the GNS3 cloud so it can bridge to a public network for updates and software packages.  The second interface connects to the Juniper on the internal lab network:

![ubuntu cloud guest](/images/ubuntucloudguest.png)

Install ExaBGP on the Ubuntu server:

```
sudo apt install exabgp
```

Install the MRT Parsing Tool, which will convert the RIPE BGP data:

```
sudo apt install mrtparse
```

[RIPE](https://www.ripe.net/analyse/internet-measurements/routing-information-service-ris/ris-raw-data) generates snapshots of full routing tables from several routers around the world.

```
wget http://data.ris.ripe.net/rrc16/latest-bview.gz
```

Convert the RAW BGP data from RIPE into a format that ExaBGP can process:

```
mrt2exabgp -G -P ./latest-bview.gz > fullbgptable.py
```

I replace `next-hop` with `self` so that BGP loads the routes if the next-hop address is not reachable (updated 2020/12/29 thanks to the reader Lionel):

```
  sed -i -E "s/(next-hop ).+( nlri)/\1self\2/g" fullbgptable.py
```
Create an ExaBGP configuration file (cogent-12.cfg):

```
template {
  neighbor cogent {
    local-as 174;
    family {
      ipv4 unicast;
      ipv6 unicast;
    }
  }
}

neighbor 38.104.162.78 {
  inherit cogent;
  router-id 38.104.162.77;
  local-address 38.104.162.77;
  peer-as 2552;
}

process fullbgp {
  run /usr/bin/python3 /home/ubuntu/fullbgptable.py;
  encoder text;
}
```

Start ExaBGP:

```
ubuntu@ubuntu:~$ exabgp ./cogent-12.cfg
19:55:22 | 18047  | welcome       | Thank you for using ExaBGP
19:55:22 | 18047  | version       | 4.0.2-1c737d99
19:55:22 | 18047  | interpreter   | 3.6.9 (default, Jul 17 2020, 12:50:27)  [GCC 8.4.0]
19:55:22 | 18047  | os            | Linux ubuntu 4.15.0-20-generic #21-Ubuntu SMP Tue Apr 24 06:16:15 UTC 2018 x86_64
19:55:22 | 18047  | installation  |
19:55:22 | 18047  | advice        | environment file missing
19:55:22 | 18047  | advice        | generate it using "exabgp --fi > /etc/exabgp/exabgp.env"
19:55:22 | 18047  | cli           | could not find the named pipes (exabgp.in and exabgp.out) required for the cli
19:55:22 | 18047  | cli           | we scanned the following folders (the number is your PID):
19:55:22 | 18047  | cli control   |  - /run/1000/
19:55:22 | 18047  | cli control   |  - /run/
19:55:22 | 18047  | cli control   |  - /var/run/1000/
19:55:22 | 18047  | cli control   |  - /var/run/
19:55:22 | 18047  | cli control   |  - /run/1000/
19:55:22 | 18047  | cli control   |  - /run/
19:55:22 | 18047  | cli control   |  - /var/run/1000/
19:55:22 | 18047  | cli control   |  - /var/run/
19:55:22 | 18047  | cli control   | please make them in one of the folder with the following commands:
19:55:22 | 18047  | cli control   | > mkfifo /home/ubuntu/run/exabgp.{in,out}
19:55:22 | 18047  | cli control   | > chmod 600 /home/ubuntu/run/exabgp.{in,out}
19:55:22 | 18047  | cli control   | > chown 1000:1000 /home/ubuntu/run/exabgp.{in,out}
19:55:22 | 18047  | configuration | performing reload of exabgp 4.0.2-1c737d99
19:55:22 | 18047  | reactor       | loaded new configuration successfully
19:55:22 | 18047  | reactor       | connected to peer-1 with outgoing-1 38.104.162.77-38.104.162.78
igp as-path [ 52320 6461 2152 18558 ] med 0 community 52320:11912
19:55:23 | 18047  | api           | route added to neighbor 38.104.162.78 local-ip 38.104.162.77 local-as 174 peer-as 2552 router-id 38.104.162.77 family-allowed in-open : 204.34.103.0/24 next-hop self origin igp as-path [ 52320 174 3356 3908 721 27066 3954 3954 ] med 0 community 52320:11912
19:55:23 | 18047  | api           | route added to neighbor 38.104.162.78 local-ip 38.104.162.77 local-as 174 peer-as 2552 router-id 38.104.162.77 family-allowed in-open : 204.34.108.0/24 next-hop self origin igp as-path [ 52320 174 3356 3908 721 27066 3954 3954 ] med 0 community 52320:11912
19:55:23 | 18047  | api           | route added to neighbor 38.104.162.78 local-ip 38.104.162.77 local-as 174 peer-as 2552 router-id 38.104.162.77 family-allowed in-open : 204.100.168.0/24 next-hop self origin igp as-path [ 52320 6461 2152 18558 ] med 0 community 52320:11912
19:55:23 | 18047  | api           | route added to neighbor 38.104.162.78 local-ip 38.104.162.77 local-as 174 peer-as 2552 router-id 38.104.162.77 family-allowed in-open : 204.100.169.0/24 next-hop self origin igp as-path [ 52320 6461 2152 18558 ] med 0 community 52320:11912
19:55:23 | 18047  | api           | route added to neighbor 38.104.162.78 local-ip 38.104.162.77 local-as 174 peer-as 2552 router-id 38.104.162.77 family-allowed in-open : 204.34.109.0/24 next-hop self origin igp as-path [ 52320 174 3356 3908 721 27066 3954 3954 ] med 0 community 52320:11912
....
```

Validate the the Juniper and the ExaBGP instance are peered and exchanging routes (Note: the example below uses a partial routing table so that the system starts up faster):

```
jemurray@900w-lab> show bgp neighbor 38.104.162.77
Peer: 38.104.162.77+39163 AS 174 Local: 38.104.162.78+179 AS 2552
  Group: COGENT                Routing-Instance: master
  Forwarding routing-instance: master
  Type: External    State: Established    Flags: <Sync>
  Last State: OpenConfirm   Last Event: RecvKeepAlive
  Last Error: None
  Export: [ BGP_POLICY_TO_COGENT_V4 ]
  Import: [ RPKI_VALIDATION_POLICY FILTER_INVALID_V4 BGP_POLICY_FROM_COGENT_V4 ]
  Options: <Preference RemovePrivateAS GracefulRestart LogUpDown AddressFamily PeerAS Refresh>
  Options: <GracefulShutdownRcv>
  Remove-private options: all
  Address families configured: inet-unicast
  Holdtime: 90 Preference: 170
  Graceful Shutdown Receiver local-preference: 0
  Number of flaps: 91
  Last flap event: TransportError
  Peer ID: 38.104.162.77   Local ID: 128.252.1.1     Active Holdtime: 90
  Keepalive Interval: 30         Group index: 1    Peer index: 0    SNMP index: 6
  I/O Session Thread: bgpio-0 State: Enabled
  BFD: disabled, down
  Local Interface: ge-0/0/6.0
  NLRI for restart configured on peer: inet-unicast
  NLRI advertised by peer: inet-unicast inet6-unicast
  NLRI for this session: inet-unicast
  Peer does not support Refresh capability
  Restart time configured on the peer: 120
  Stale routes from peer are kept for: 300
  Peer does not support Restarter functionality
  Peer does not support Receiver functionality
  Peer does not support LLGR Restarter or Receiver functionality
  Peer supports 4 byte AS extension (peer-as 174)
  Peer does not support Addpath
  NLRI(s) enabled for color nexthop resolution: inet-unicast
  Table inet.0 Bit: 20000
    RIB State: BGP restart is complete
    Send state: in sync
    Active prefixes:              17429
    Received prefixes:            17576
    Accepted prefixes:            17429
    Suppressed due to damping:    0
    Advertised prefixes:          4
  Last traffic (seconds): Received 4    Sent 1    Checked 84
  Input messages:  Total 8710   Updates 8705    Refreshes 0     Octets 706607
  Output messages: Total 5      Updates 2       Refreshes 0     Octets 166
  Output Queue[1]: 0            (inet.0, inet-unicast)
  ```

Check the routing table to validate routes are installed in the FIB:

```
jemurray@900w-lab> show route receive-protocol bgp 38.104.162.77

inet.0: 17590 destinations, 17590 routes (17443 active, 0 holddown, 147 hidden)
Restart Complete
  Prefix                  Nexthop              MED     Lclpref    AS path
* 204.2.19.0/24           38.104.162.77        0                  52320 1299 397111 I
* 204.2.30.0/23           38.104.162.77        110                52320 8455 8455 8455 8455 8283 15562 I
* 204.2.35.0/24           38.104.162.77        0                  52320 1299 2914 394563 I
* 204.2.36.0/24           38.104.162.77        0                  52320 1299 2914 16986 I
* 204.2.57.0/24           38.104.162.77        110                52320 174 64277 ?
* 204.2.64.0/20           38.104.162.77        0                  52320 1299 57866 64404 I
* 204.2.135.0/24          38.104.162.77        110                52320 6461 46582 I
* 204.2.140.0/22          38.104.162.77        0                  52320 1299 2914 1225 I
* 204.2.186.0/24          38.104.162.77        110                52320 6461 397341 I
* 204.2.198.0/24          38.104.162.77        0                  52320 1299 2914 395563 I
```
