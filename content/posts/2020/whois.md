---
title: "The many queries of whois"
date: 2020-09-28T15:36:55-05:00
toc: false
images:
categories:
  - tech
tags: 
  - whois
---

# Summary

The `whois` command can return more than domain name information.   Abuse contacts, subnet owners, ISP peerings, ASN mappings, are some of the databases `whois` can query.  

Depending on specific software and version, the `whois` command may automatically determine the type of information requested.   On macOS, looking up subnet owner, ASN, or domain name does not require any special options.   For clients that do not support this feature, the `-h <server>` option must be specified.  

# Abuse Contact

Retrieve the abuse contact for a domain:

```
jemurray@shell:~$ whois -h whois.abuse.net wustl.edu
```

Full output:

```
jemurray@shell:~$ whois -h whois.abuse.net wustl.edu
noc@wustl.edu (for wustl.edu)
abuse@wustl.edu (for wustl.edu)
nts-admin@wustl.edu (for wustl.edu)
```

# Subnet

Subnet owner lookup:

```
whois 128.252.0.1
```

Specify a server which returns subnet information:

```
whois -h whois.arin.net 128.252.0.1
```

Full output:

```
jemurray@mbp-2019:~ $ whois 128.252.0.1
% IANA WHOIS server
% for more information on IANA, visit http://www.iana.org
% This query returned 1 object

refer:        whois.arin.net

inetnum:      128.0.0.0 - 128.255.255.255
organisation: Administered by ARIN
status:       LEGACY

whois:        whois.arin.net

changed:      1993-05
source:       IANA

# whois.arin.net

NetRange:       128.252.0.0 - 128.252.255.255
CIDR:           128.252.0.0/16
NetName:        WASHINGTON-U
NetHandle:      NET-128-252-0-0-1
Parent:         NET128 (NET-128-0-0-0-0)
NetType:        Direct Assignment
OriginAS:       AS2552
Organization:   Washington University (WASHIN-Z)
RegDate:        1987-06-03
Updated:        2018-12-18
Comment:        https://it.wustl.edu/
Ref:            https://rdap.arin.net/registry/ip/128.252.0.0
```


# ASN Number

ASN owner lookup:

```
whois 2552
```

Specify a server that supports ASN lookups:

```
whois -h whois.arin.net 2552
```

Full output:

```
jemurray@mbp-2019:~ $ whois 2552
% IANA WHOIS server
% for more information on IANA, visit http://www.iana.org
% This query returned 1 object

refer:        whois.arin.net

as-block:     2137-2584
organisation: Assigned by ARIN

whois:        whois.arin.net
descr:        Assigned by ARIN

source:       IANA

# whois.arin.net

ASNumber:       2552
ASName:         WUSTL-ASN
ASHandle:       AS2552
RegDate:        1993-03-16
Updated:        2012-04-02
Ref:            https://rdap.arin.net/registry/autnum/2552
```

# IP to ASN

Map an IP address to AS number:

```
whois -h whois.cymru.com 128.252.0.1
```

Full output:

```
jemurray@shell:~$ whois -h whois.cymru.com 128.252.0.1
AS      | IP               | AS Name
2552    | 128.252.0.1      | WUSTL-ASN, US
```

# IP to Internet Peering

Determine what ISPs an IP address peers with:

```
whois -h v4-peer.whois.cymru.com 128.252.0.1
```

Full output:

```
jemurray@shell:~$ whois -h v4-peer.whois.cymru.com 128.252.0.1
PEER_AS | IP               | AS Name
3356    | 128.252.0.1      | LEVEL3, US
19782   | 128.252.0.1      | INDIANAGIGAPOP, US
20115   | 128.252.0.1      | CHARTER-20115, US
```

# AS, BGP, and Registry

```
whois -h whois.cymru.com -v 128.252.0.1
```

```
jemurray@shell:~$ whois -h whois.cymru.com -v 128.252.0.1
Warning: RIPE flags used with a traditional server.
AS      | IP               | BGP Prefix          | CC | Registry | Allocated  | AS Name
2552    | 128.252.0.1      | 128.252.0.0/16      | US | arin     | 1987-06-03 | WUSTL-ASN, US
```


# Domain

Return the owner of a domain name:

```
whois wustl.edu
```

Full output:

```
jemurray@mbp-2019:~ $ whois wustl.edu
% IANA WHOIS server
% for more information on IANA, visit http://www.iana.org
% This query returned 1 object

refer:        whois.educause.edu

-------------------------------------------------------------

Domain Name: WUSTL.EDU

Registrant:
	Washington University in St. Louis
	Network Technology Services
	One Brookings Drive Campus Box 1048
	St. Louis, MO 63130-4899
	US

Administrative Contact:
	Domain Admin
	Network Technology Services
	Washington University
	1 Brookings Dr, Campus Box 1048
	St. Louis, MO 63130-4899
	US
	+1.3149357048
	nts-admin@wustl.edu

Technical Contact:

	Network Technology Services
	Washington University
	1 Brookings Dr Campus Box 1048
	St. Louis, MO 63130-4899
	US
	+1.3149357048
	noc@wustl.edu

Name Servers:
	NS01.IP.WUSTL.EDU
	NS00.IP.WUSTL.EDU
	NS04.IP.WUSTL.EDU
	NS03.IP.WUSTL.EDU
	NS02.IP.WUSTL.EDU

Domain record activated:    07-Dec-1987
Domain record last updated: 26-Sep-2020
Domain expires:             31-Jul-2021
```