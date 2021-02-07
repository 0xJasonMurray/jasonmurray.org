---
title: "Provisioning BGP filters with bgpq3"
date: 2020-10-07T12:56:14-05:00
toc: false
images:
categories:
  - tech
tags: 
  - bgp
  - irr
  - bgpq3
---

# Overview

This week, we learned how to create [BGP prefix filter lists](/posts/2020/juniperbgpimportfilter/).  Today we discuss tools to aid in the creation of prefix-lists using publicly available information from the IRR database [RADb](https://www.radb.net/).

These tools are geared more towards service providers, but is worth understanding the techniques and filtering mechanisms providers use to prevent network hijacking in the global routing table.

# Details

`bgpq3` queries the RADb IRR and returns routes as documented by the ASN / network owners.  In this example, Washington University in St. Louis publishes the prefixes they announce in an IRR.  By using `bgpq3` this information is automatically gathered and presented in a format that can be pasted directly into the native router filtering language:

```
jemurray@mbp-2019:~ $ bgpq3 -Jl wustl AS2552
policy-options {
replace:
 prefix-list wustl {
    65.254.96.0/19;
    65.254.109.0/24;
    128.25.0.0/16;
    128.252.0.0/16;
    128.252.48.0/24;
    128.252.182.0/24;
    192.31.46.0/24;
 }
}
```

Comparing the prefix-list returned from `bgpq3` to live router data, we confirm the data is accurate:

```
router-0> show route advertising-protocol bgp
  Prefix		  Nexthop	       MED     Lclpref    AS path
* 65.254.96.0/19          Self                                    2552 2552 [2552] I
* 65.254.109.0/24         Self                                    I
* 128.252.0.0/16          Self                                    2552 2552 [2552] I
* 128.252.25.0/24         Self                                    I
* 128.252.48.0/24         Self                                    I
* 192.31.46.0/24          Self                                    2552 2552 [2552] I
```

