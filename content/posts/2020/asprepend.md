---
title: "Automatic AS prepending with BGP communities on JunOS"
date: 2020-10-12T10:11:07-05:00
toc: false
images:
categories:
  - tech
tags: 
  - bgp
  - juniper
  - asn
  - junos
  - communities
---

# Overview

The purpose of this lab is to show how Juniper policy-options can be used to automatically increase AS path length based on community tags attached to routes sent over BGP.

BGP AS path length is one option commonly used to determine the best path to a route on the Internet.  The AS path length can also be used to influence how traffic is returned when a specific path is required.  

In the diagram below, we see the network `10.103.0.0/16` is announced to 2 upstream providers.   From the `Internet's` perspective there are 2 equal cost return paths.  To avoid returning through `LogicalSystem-2`, we can increase the length of the AS path through this hop.  BGP will select the route with the shortest hop count, eliminating `LogicalSystem-2` as a viable router.  

![Shorter AS path is preferred](/images/shorteraspathpreferred.png)

In this lab there are 2 routers `LogicalSystem-1` and `LogicalSystem-2`.  There is no `router-3` as seen in the diagram above:

![juniper lab overview](/images/juniperprepend.png)

# Details

## Juniper Config

We are going to configure LogicalSystem-1 to tag 3 static routes with different community strings.   The community strings have the following meaning:

- 65001:0 = Prepend origin AS 1 times
- 65002:0 = Prepend origin AS 2 times
- 65003:0 = Prepend origin AS 3 times

When LogicalSystem-2 receives BGP routes tagged with these community strings, a routing policy will automatically prepend the origin AS as noted by the community string.


### LogicalSystem-1

Add a community tag to each of the static routes configured on `LogicalSystem-1`:

```
set logical-systems LogicalSystem-1 routing-options static route 10.101.0.0/16 community 65001:0
set logical-systems LogicalSystem-1 routing-options static route 10.102.0.0/16 community 65002:0
set logical-systems LogicalSystem-1 routing-options static route 10.103.0.0/16 community 65003:0
```


### LogicalSystem-2

Create a routing policy on `LogicalSystem-2` which looks for the trigger community tag. Then prepend the appropriate number of origin AS numbers:

```
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 1 from protocol bgp
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 1 from community prepend-1
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 1 then as-path-expand last-as count 1
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 2 from protocol bgp
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 2 from community prepend-2
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 2 then as-path-expand last-as count 2
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 3 from protocol bgp
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 3 from community prepend-3
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 3 then as-path-expand last-as count 3
```

In order to match a community string, a policy-option must be created: 

```
set logical-systems LogicalSystem-2 policy-options community prepend-1 members 65001:0
set logical-systems LogicalSystem-2 policy-options community prepend-2 members 65002:0
set logical-systems LogicalSystem-2 policy-options community prepend-3 members 65003:0
```

Finally, apply the routing policy to the BGP import policy:

```
set logical-systems LogicalSystem-2 protocols bgp group remote-peers-group-a import automatic-prepend
```



## Validate Automatic Prepending

Before prepending, we see all routes learned from LogicalSystem-1 are a single hop away:

```
jemurray@LogicalSystem-1> show route logical-system LogicalSystem-2

inet.0: 14 destinations, 14 routes (14 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.100.0.0/16      *[BGP/170] 00:18:19, localpref 100
                      AS path: 64512 I, validation-state: unverified
                    >  to 192.168.99.0 via lt-0/0/0.201
10.101.0.0/16      *[BGP/170] 00:18:19, localpref 100
                      AS path: 64512 I, validation-state: unverified
                    >  to 192.168.99.0 via lt-0/0/0.201
10.102.0.0/16      *[BGP/170] 00:18:19, localpref 100
                      AS path: 64512 I, validation-state: unverified
                    >  to 192.168.99.0 via lt-0/0/0.201
10.103.0.0/16      *[BGP/170] 00:18:19, localpref 100
                      AS path: 64512 I, validation-state: unverified
                    >  to 192.168.99.0 via lt-0/0/0.201
10.104.0.0/16      *[BGP/170] 00:18:19, localpref 100
                      AS path: 64512 I, validation-state: unverified
                    >  to 192.168.99.0 via lt-0/0/0.201
10.105.0.0/16      *[BGP/170] 00:00:37, localpref 100
                      AS path: 64512 I, validation-state: unverified
                    >  to 192.168.99.0 via lt-0/0/0.201
```

After the automatic prepending routing policy is applied, route `101` has an AS path length of 2, `102` has an AS path length of 3, and `103` has an AS path length of 4:

```
jemurray@LogicalSystem-1> show route logical-system LogicalSystem-2

inet.0: 14 destinations, 14 routes (14 active, 0 holddown, 0 hidden)
+ = Active Route, - = Last Active, * = Both

10.100.0.0/16      *[BGP/170] 00:20:28, localpref 100
                      AS path: 64512 I, validation-state: unverified
                    >  to 192.168.99.0 via lt-0/0/0.201
10.101.0.0/16      *[BGP/170] 00:00:11, localpref 100
                      AS path: 64512 64512 I, validation-state: unverified
                    >  to 192.168.99.0 via lt-0/0/0.201
10.102.0.0/16      *[BGP/170] 00:00:11, localpref 100
                      AS path: 64512 64512 64512 I, validation-state: unverified
                    >  to 192.168.99.0 via lt-0/0/0.201
10.103.0.0/16      *[BGP/170] 00:00:11, localpref 100
                      AS path: 64512 64512 64512 64512 I, validation-state: unverified
                    >  to 192.168.99.0 via lt-0/0/0.201
10.104.0.0/16      *[BGP/170] 00:20:28, localpref 100
                      AS path: 64512 I, validation-state: unverified
                    >  to 192.168.99.0 via lt-0/0/0.201
10.105.0.0/16      *[BGP/170] 00:02:46, localpref 100
                      AS path: 64512 I, validation-state: unverified
                    >  to 192.168.99.0 via lt-0/0/0.201
```


Displaying the more detailed route information shows the AS path and the community strings associated with the route:

```
jemurray@LogicalSystem-1> show route 10.103.0.0 detail logical-system LogicalSystem-2

inet.0: 14 destinations, 14 routes (14 active, 0 holddown, 0 hidden)
10.103.0.0/16 (1 entry, 1 announced)
        *BGP    Preference: 170/-101
                Next hop type: Router, Next hop index: 684
                Address: 0xc0f1848
                Next-hop reference count: 12
                Source: 192.168.99.0
                Next hop: 192.168.99.0 via lt-0/0/0.201, selected
                Session Id: 0x147
                State: <Active Ext>
                Local AS: 64513 Peer AS: 64512
                Age: 9:47
                Validation State: unverified
                Task: BGP_64512.192.168.99.0
                Announcement bits (1): 1-KRT
                AS path: 64512 64512 64512 64512 I
                Communities: 65003:0
                Accepted
                Localpref: 100
                Router ID: 192.168.99.0
```


# Appendix: Full Configs

Many of these configurations are built off of previous labs.  There may be extra parts not relevant to the examples above.

## LogicalSystem-1 full router config:

```
jemurray@juniper-1> show configuration logical-systems LogicalSystem-1 | display set
set logical-systems LogicalSystem-1 interfaces lt-0/0/0 unit 101 description "LogicalSystem-2 P2P Interface"
set logical-systems LogicalSystem-1 interfaces lt-0/0/0 unit 101 encapsulation ethernet
set logical-systems LogicalSystem-1 interfaces lt-0/0/0 unit 101 peer-unit 201
set logical-systems LogicalSystem-1 interfaces lt-0/0/0 unit 101 family inet address 192.168.99.0/31
set logical-systems LogicalSystem-1 protocols bgp group remote-peers-group-b type external
set logical-systems LogicalSystem-1 protocols bgp group remote-peers-group-b export export-local-prefixes
set logical-systems LogicalSystem-1 protocols bgp group remote-peers-group-b neighbor 192.168.99.1 import import-prefixes_192.168.99.1
set logical-systems LogicalSystem-1 protocols bgp group remote-peers-group-b neighbor 192.168.99.1 peer-as 64513
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.100.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.101.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.102.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.103.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.104.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list local-prefixes 10.105.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list import-prefixes_192.168.99.1 10.200.0.0/16
set logical-systems LogicalSystem-1 policy-options prefix-list import-prefixes_192.168.99.1 10.201.0.0/16
set logical-systems LogicalSystem-1 policy-options policy-statement export-local-prefixes term 1 from prefix-list local-prefixes
set logical-systems LogicalSystem-1 policy-options policy-statement export-local-prefixes term 1 then accept
set logical-systems LogicalSystem-1 policy-options policy-statement import-prefixes_192.168.99.1 term 1 from protocol bgp
set logical-systems LogicalSystem-1 policy-options policy-statement import-prefixes_192.168.99.1 term 1 from prefix-list import-prefixes_192.168.99.1
set logical-systems LogicalSystem-1 policy-options policy-statement import-prefixes_192.168.99.1 term 1 then accept
set logical-systems LogicalSystem-1 policy-options policy-statement import-prefixes_192.168.99.1 term 2 then reject
set logical-systems LogicalSystem-1 routing-options static route 10.100.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.101.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.101.0.0/16 community 65001:0
set logical-systems LogicalSystem-1 routing-options static route 10.102.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.102.0.0/16 community 65002:0
set logical-systems LogicalSystem-1 routing-options static route 10.103.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.103.0.0/16 community 65003:0
set logical-systems LogicalSystem-1 routing-options static route 10.104.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options static route 10.105.0.0/16 discard
set logical-systems LogicalSystem-1 routing-options autonomous-system 64512
```

## LogicalSystem-2 full router config

```
jemurray@juniper-1> show configuration logical-systems LogicalSystem-2 | display set
set logical-systems LogicalSystem-2 interfaces lt-0/0/0 unit 201 description "LogicalSystem-1 P2P Interface"
set logical-systems LogicalSystem-2 interfaces lt-0/0/0 unit 201 encapsulation ethernet
set logical-systems LogicalSystem-2 interfaces lt-0/0/0 unit 201 peer-unit 101
set logical-systems LogicalSystem-2 interfaces lt-0/0/0 unit 201 family inet address 192.168.99.1/31
set logical-systems LogicalSystem-2 protocols bgp group remote-peers-group-a type external
set logical-systems LogicalSystem-2 protocols bgp group remote-peers-group-a import automatic-prepend
set logical-systems LogicalSystem-2 protocols bgp group remote-peers-group-a export export-local-prefixes
set logical-systems LogicalSystem-2 protocols bgp group remote-peers-group-a neighbor 192.168.99.0 peer-as 64512
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.200.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.201.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.202.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.203.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.204.0.0/16
set logical-systems LogicalSystem-2 policy-options prefix-list local-prefixes 10.205.0.0/16
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 1 from protocol bgp
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 1 from community prepend-1
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 1 then as-path-expand last-as count 1
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 2 from protocol bgp
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 2 from community prepend-2
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 2 then as-path-expand last-as count 2
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 3 from protocol bgp
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 3 from community prepend-3
set logical-systems LogicalSystem-2 policy-options policy-statement automatic-prepend term 3 then as-path-expand last-as count 3
set logical-systems LogicalSystem-2 policy-options policy-statement export-local-prefixes term 1 from prefix-list local-prefixes
set logical-systems LogicalSystem-2 policy-options policy-statement export-local-prefixes term 1 then accept
set logical-systems LogicalSystem-2 policy-options community prepend-1 members 65001:0
set logical-systems LogicalSystem-2 policy-options community prepend-2 members 65002:0
set logical-systems LogicalSystem-2 policy-options community prepend-3 members 65003:0
set logical-systems LogicalSystem-2 routing-options static route 10.200.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.201.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.202.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.203.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.204.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options static route 10.205.0.0/16 discard
set logical-systems LogicalSystem-2 routing-options autonomous-system 64513
```

