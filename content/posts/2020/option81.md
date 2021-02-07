---
title: "FQDN DNS updates using DHCP Option 81"
date: 2020-09-11T08:43:02-05:00
toc: false
images:
categories:
  - tech
tags: 
  - dhcp
  - option81
---

# Overview

Integrating DHCP with a DNS server, allows devices to register a fully qualified domain when requesting an IP address.  This type of automatic DNS update, is known as dynamic DNS or DDNS.  Dynamic DNS is helpful because humans find it easier to remember host.example.com then 192.168.212.123.  If IPv6 is used, dynamic DNS is even more important.  Not many people can easily remember an IPv6 address: 2604:3e00:123:4534:123:2332::1.

# Options

There are 2 primary types of DDNS updates the DHCP server can perform:

- Combining the hostname with the domain name assigned by the DHCP server.
- Using the fully qualified hostname retrieved from the client to update DNS.

# DHCP assigned domain

When the DHCP server assigns the domain, the client does not have a choice in the selection.  If the DHCP server is configured to update DNS with the domain `example.com`, the client will make the DHCP request and send along its hostname.   The DHCP server will combine the client hostname and the statically configured domain to update the DNS record with:

- host.example.com


# Client assigned domain

Allowing the client to send a fully qualified DNS entry to the DHCP server is known as `option 81`.  When the client requests a DHCP address, the fully qualified domain is sent along with the request.  The DHCP server then updates DNS using the FQDN on behalf of the client.   If the clients DNS name is `host.jasonmurray.org` the DHCP server will update DNS with:

- host.jasonmurray.org

