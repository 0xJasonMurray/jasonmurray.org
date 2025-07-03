---
title: "Using the sleep command on Cisco NX-OS to correlate log file events"
date: 2020-07-05T06:08:30-05:00
categories:
  - tech
tags: [til, nxos, sleep]
---

Inserting a set amount of time by using the `sleep XX` statement between commands, can help to correlate multiple events in a log file by measuring the exact time between logged events.

Inserting a 5 second delay between commands on Cisco NX-OS:

```
nx7k-MPLS(config-if)# shut ; sleep 5 ; no shut
```

There was exactly 5 seconds between shutting and no-shutting an interface which helps to confirm the withdrawal and adding of routes in the IS-IS logs below:

```
2020 Apr 25 12:56:55.886160 isis isis-config [22629]: [1677]: Delete direct prefix 192.168.247.154/32 on loopback0 for L2 MT-0 Transition:None
2020 Apr 25 12:57:00.913744 isis isis-config [22629]: [1677]: Add direct prefix 192.168.247.154/32 on loopback0 for L2 MT-0 metric 10 Transition:None
```


