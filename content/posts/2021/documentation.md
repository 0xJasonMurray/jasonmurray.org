---
title: "Internal and Documentation IP Blocks"
date: 2021-03-25T16:50:27-05:00
toc: false
images:
categories:
  - tech
tags: 
  - documentation
  - networking
  - rfc1918
  - rfc4193
  - rfc5737
  - rfc3849
description: IP address allocations for the purpose of internal routing or documentation.
---

## Overview

Prior to 2010, the IP block 1.0.0.0/8 was considered unassigned before its allocation to ARIN. Many organizations considered this unassigned space available for private use within their products or documentation.

In 2018 Cloudflare purchased the 1.1.1.0/24 allocation for their anycast DNS service. This is when they found out how many network connected devices were misconfigured with addresses allocated from this block, most notably the 1.1.1.1 address. Early measurements documented [~10Gb/s of background traffic](https://blog.cloudflare.com/fixing-reachability-to-1-1-1-1-globally/).

It did not have to be this way. Documentation and Internet IP space existed since 1995 with the allocation of IPv4 local address space. No organization should have been using IP space that would eventually make its way to the allocated pool. Even Cisco, the largest networking provider in the world, [misused the 1.1.1.1 address for the captive portal on their wireless lan controllers](https://www.cisco.com/c/en/us/support/docs/wireless-mobility/wireless-lan-wlan/213535-wlc-virtual-ip-address-1-1-1-1.html). 

The following post documents IP space available for internal and documentation use. The address blocks along with the RFC numbers are detailed below.



## Private IPv4 Space

The following IPv4 unicast addresses are globally unique and intended for local communications. They are not expected to be routable on the global Internet.  They are routable inside of a more limited area such as a site.  They may also be routed between a limited set of sites.

[RFC1918](https://tools.ietf.org/html/rfc1918) and [RFC6598](https://tools.ietf.org/html/rfc6598):

- 10.0.0.0/8 (RFC1918)
- 172.16.0.0/12 (RFC1918)
- 192.168.0.0/16 (RFC1918)
- 100.64.0.0/10 (RFC6598)

## Private IPv6 Space

The following IPv6 unicast addresses are globally unique and intended for local communications.  These addresses are called Unique Local IPv6 Unicast Addresses or Local IPv6 addresses.  They are not expected to be routable on the global Internet.  They are routable inside of a more limited area such as a site.  They may also be routed between a limited set of sites.

[RFC4193](https://tools.ietf.org/html/rfc4193)

- fc00::/7

## Documentation IPv4 Address Space

Three IPv4 address blocks are provided for use in documentation.  The use of designated address ranges for documentation and examples reduces the likelihood of conflicts and confusion arising from the use of addresses assigned for some other purpose.

[RFC5737](https://tools.ietf.org/html/rfc5737)

- 192.0.2.0/24
- 198.51.100.0/24
- 203.0.113.0/24

## Documentation IPv6 Address Space

To reduce the likelihood of conflict and confusion when relating documented examples to deployed systems, an IPv6 unicast address prefix is reserved for use in examples in RFCs, books, documentation, and the like.  Since site-local and link-local unicast addresses have special meaning in IPv6, these addresses cannot be used in many example situations.

[RFC3849](https://tools.ietf.org/html/rfc3849)

- 2001:DB8::/32