---
title: "Using BGP to prefer Internet2 Connected Peers"
date: 2020-05-02T10:22:58-05:00
categories:
  - tech
tags: [lfp, internet2, bgp, routing, internet]
---

Many universities and research institutions are connected to [Internet2](https://www.internet2.edu/), a high-speed network that is similar to the commodity [Internet](https://en.wikipedia.org/wiki/Internet) but designed to support [large data science network transfers](https://fasterdata.es.net/science-dmz/).

By using [BGP](https://tools.ietf.org/html/rfc4271), institutions connected to [multiple service providers and Internet2](https://www.juniper.net/documentation/en_US/junos/topics/reference/general/routing-protocols-address-representation.html) can define policies that prefer prefixes learned over Internet2 peerings.


## Influencing Outbound Traffic using Local-Preference

This section provides configuration examples of `local-preference` as the mechanism used to influence outbound traffic to prefer Internet2 connected peers over commodity Internet-connected peers.

Example interface configuration from a Juniper router:

```
    xe-1/2/0 {
        description "I2 Peering";
        flexible-vlan-tagging;
        mtu 9192;
        encapsulation flexible-ethernet-services;
        unit 944 {
            description "Internet2 - Member Peering Services";
            vlan-id 944;
            family inet {
                mtu 9000;
                filter {
                    input-list INGRESS_I2_POLICY_V4;
                    output-list EGRESS_I2_POLICY_V4;
                }
                address 192.168.254.142/30;
            }
        }
```

BGP neighbor policy, this is where the policy statment is applied to the neighbor:

```
neighbor 192.168.254.141 {
    import [ RPKI_VALIDATION_POLICY FILTER_INVALID_V4 BGP_POLICY_FROM_INTERNET2_V4 ];
    export BGP_POLICY_TO_INTERNET2_V4;
}
```

Add 100 to `local preference`:

```
policy-statement BGP_POLICY_FROM_INTERNET2_V4 {
    term ACCEPTED_ROUTES {
        then {
            local-preference add 100;
            community add COMMUNITY_FROM_INTERNET2;
        }
    }
}
```

Validate Internet2 routes are preferred:

```
edge-router> show route 1.1.1.1

inet.0: 805841 destinations, 2187628 routes (805653 active, 0 holddown, 366259 hidden)
Restart Complete
+ = Active Route, - = Last Active, * = Both

1.1.1.0/24         *[BGP/170] 6d 04:58:40, MED 0, localpref 200
                      AS path: 19782 13335 I, validation-state: valid
                    > to 192.168.254.141 via xe-1/2/0.944
                    [BGP/170] 2w0d 13:02:03, MED 0, localpref 100
                      AS path: 3356 13335 I, validation-state: valid
                    > to 192.168.92.177 via xe-1/2/3.0
                    [BGP/170] 19:07:37, MED 10010, localpref 100
                      AS path: 174 13335 I, validation-state: valid
                    > to 192.168.162.77 via xe-0/0/2.0
```

## Influencing Return Traffic using AS Prepending

AS prepending is used to influence return traffic to prefer Internet2 connected peers, by making the AS path longer on commodity connected Internet peers.

```
    policy-statement BGP_POLICY_TO_COMMODITY_V4 {
        term advertised_routes {
            from {
                prefix-list-filter ADVERTISED_V4_PREFIXES exact;
            }
            then {
                as-path-prepend "2552 2552";
                accept;
            }
        }
```

Using a public [route server](http://www.routeservers.org/), validate the AS number has been prepending two times.

```
route-server> show bgp ipv4 128.252.0.0/16
BGP routing table entry for 128.252.0.0/16
Paths: (23 available, best #11, table Default-IP-Routing-Table)
  Not advertised to any peer
  20115 2552 2552 2552
    216.66.24.70 from 216.218.252.82 (216.218.252.169)
      Origin IGP, metric 0, localpref 140, valid, internal
      Originator: 216.218.252.169, Cluster list: 216.218.252.82 216.218.252.184
      Last update: Wed Apr 29 15:57:23 2020
```


