---
title: "Mapping the chain of DNS servers from root to authoritative with dig"
date: 2021-02-23T15:47:43-06:00
toc: false
images:
categories:
  - tech
tags: 
  - dns
  - dig
description: Display the entire chain of DNS servers with the dig +trace command.
---

## Key Points

- The Domain Name System (DNS) turns domain names (jasonmurray.org) into IP addresses (104.21.41.57)
- Root name servers point to servers that resolve top level domains like .com, .net, and .org
- Second level name servers point to authoritative servers that resolve the second part of the domain such as jasonmurray, example, microsoft, and apple
- Authoritative name servers resolve fully qualified domains such as jasonmurray.org, example.com, and microsoft.com
- `dig +trace jasonmurray.org` displays the entire sequence of DNS servers required to resolve a domain name from `root` to `authoratative`

## Overview

The `dig +trace` maps out all DNS servers required to resolve a DNS domain name. Typically only necessary when troubleshooting domain resolution issues.  For curious minded individuals, this tool helps to explain the role each DNS server plays in name resolution.


## Details

Trace all the DNS servers necessary to resolve the domain `jasonmurray.org`:

```text
jemurray@shell:~$ dig +trace jasonmurray.org

; <<>> DiG 9.11.5-P4-5.1+deb10u2-Debian <<>> +trace jasonmurray.org
;; global options: +cmd
.			82734	IN	NS	a.root-servers.net.
.			82734	IN	NS	b.root-servers.net.
.			82734	IN	NS	c.root-servers.net.
.			82734	IN	NS	d.root-servers.net.
.			82734	IN	NS	e.root-servers.net.
.			82734	IN	NS	f.root-servers.net.
.			82734	IN	NS	g.root-servers.net.
.			82734	IN	NS	h.root-servers.net.
.			82734	IN	NS	i.root-servers.net.
.			82734	IN	NS	j.root-servers.net.
.			82734	IN	NS	k.root-servers.net.
.			82734	IN	NS	l.root-servers.net.
.			82734	IN	NS	m.root-servers.net.
.			82734	IN	RRSIG	NS 8 0 518400 20210221170000 20210208160000 42351 . JWnwNUp4kLm648L7JBzeiFOCM9ogRPM5zHTeNTuEYtmCvJoRxMzUcZ31 hf+M+hBr1/+qrjgvy11cRX/Zn/CuwgPAZKb0eMGQ59PqcqDrBwdSVCgA 8b5TQFHiv4Hc/72+ZgRsHghZyEAzGeBcfQuzw5QwW6mqowqmZV7nVb8o FEIf1rmM2WoQtvBQPQEdGFQDNGbaMgnXGieTKly1j8AOAs23UnH6Edih EA8fiSeqoLfowmqXdpdjfHxSWxbk8fgNUungDKPhoEhsr7xDzJfqUUtn t/XHo19nxn4icnEKaZOjMxCZwf193nsOpst8mHUFhIogRBhcuROVKpM+ lXm1BA==
;; Received 525 bytes from 2001:4860:4860::8844#53(2001:4860:4860::8844) in 2 ms

org.			172800	IN	NS	b0.org.afilias-nst.org.
org.			172800	IN	NS	a2.org.afilias-nst.info.
org.			172800	IN	NS	b2.org.afilias-nst.org.
org.			172800	IN	NS	c0.org.afilias-nst.info.
org.			172800	IN	NS	d0.org.afilias-nst.org.
org.			172800	IN	NS	a0.org.afilias-nst.info.
org.			86400	IN	DS	26974 8 2 4FEDE294C53F438A158C41D39489CD78A86BEB0D8A0AEAFF14745C0D 16E1DE32
org.			86400	IN	RRSIG	DS 8 1 86400 20210221170000 20210208160000 42351 . DVObWMq+MnZ994jALOhgw3dncWJ+BNZcawyCWQmD2iQkQOIURnQPLg++ DYOxhxOewokdxEPqNW773c1eSliEWobGlgpo9SbDPCGdTgQHRbE9cYVW G8y0quS8NHBKEeuv2ExF4JF/Q1M26koF7yTmpA6CgpWx4iAF2PcqDmx4 klwa9aXnuNX4gxVN8fUxXLJNEnXEov7/nEilkH03uoV/GVETbKEgtW9J fkB0XgaEUGSP3IZxYEpwNND4H2T9QvoFeg86qeO1YrvTiV8+CKn0PibF P8orSw05kPzTwye9r8E0CExD3+NUO29mCLa/ngqysiWSy2l2PeHUY8FK e2OaMg==
;; Received 809 bytes from 192.112.36.4#53(g.root-servers.net) in 31 ms

jasonmurray.org.	86400	IN	NS	dawn.ns.cloudflare.com.
jasonmurray.org.	86400	IN	NS	yisroel.ns.cloudflare.com.
jasonmurray.org.	86400	IN	DS	2371 13 2 DC7BAFCE4CE0D584175D5C87BC64790748AD3D701AB8B29B373702DD 7C9A0C77
jasonmurray.org.	86400	IN	RRSIG	DS 8 2 86400 20210222153204 20210201143204 34266 org. 0gaLcbBwh67NvGzheowXbEZfjk7gxzPF/VwWwCWHHV0LlWktiUuMXlYf VTwLyL8XHMtIM6HpPBkNlhY+gxRryJn9P12GQG4e18DAlMopv0fGf+yO fHHNc2dSHg+RyHRL1XaidLoRaQBnmoemR7tJ+wyInL8kw5zAGwASF5RQ fgM=
;; Received 313 bytes from 199.19.53.1#53(c0.org.afilias-nst.info) in 1 ms

jasonmurray.org.	300	IN	A	172.67.189.117
jasonmurray.org.	300	IN	A	104.21.41.57
jasonmurray.org.	300	IN	RRSIG	A 13 2 300 20210209224721 20210207204721 34505 jasonmurray.org. uP7iGuguXFNPA3ShGvMkCHp5SIIy9ARWOXhmCqg7EhS1AvS/OkqMbu8s Ui3cVu67Cw9govnw7BUd6gQoIAKaWA==
;; Received 187 bytes from 2803:f800:50::6ca2:c3b9#53(yisroel.ns.cloudflare.com) in 4 ms
```

