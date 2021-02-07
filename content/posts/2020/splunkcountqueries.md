---
title: "Use Splunk to count DNS queries by hour"
date: 2020-09-10T16:34:38-05:00
toc: false
images:
categories:
  - tech
tags: 
  - splunk
---

Splunk search string to count DNS queries logged from Zeek by hour:

```
index="prod_infosec_zeek" source = /logs/zeek/current/dns.log  NOT rcode_name = NXDOMAIN | eval c_time=strftime(ts,"%m/%d/%y %H") | table c_time | stats count by c_time
```

Splunk results:

```
c_time	count
...
09/10/20 12	8563941
09/10/20 13	27413725
09/10/20 14	33185289
09/10/20 15	30942267
09/10/20 16	22590486
09/10/20 17	3876147
...
```

The query works by converting the EPOCH timestamp Zeek creates into the human readable format: `month/day/year hour`.  It then sorts and counts the dates to get hourly stats.