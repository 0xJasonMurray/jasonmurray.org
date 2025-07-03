---
title: "How to tell if a DNS record is a top-level zone"
date: 2020-07-07T12:05:31-05:00
toc: false
images:
categories:
  - tech
tags: 
  - dns
  - zone
  - domain
---

By querying for the [SOA](https://en.wikipedia.org/wiki/SOA_record) of a DNS record, we can determine if the answer is the top level-zone or not.

Using the `host` command, set the query option to return the SOA record.  If an answer is returned, the record is part of the top-level zone:
```
jemurray@mbp-2019:~ $ host -t soa wustl.edu
wustl.edu has SOA record ns00.ip.wustl.edu. noc.wustl.edu. 2012476365 10800 3600 2419200 900
```

Sub-domains can be top level zones:

```
jemurray@mbp-2019:~ $ host -t soa nts.wustl.edu
nts.wustl.edu has SOA record ns00.ip.wustl.edu. noc.wustl.edu. 2776394 10800 3600 2419200 900
```

If there is no SOA record, the DNS entry is a standard record and not a top-level domain:

```
jemurray@mbp-2019:~ $ host -t soa wugate.wustl.edu
wugate.wustl.edu has no SOA record
```

