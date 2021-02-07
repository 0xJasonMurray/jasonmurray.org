---
title: "Cisco 16.x reports drops in bytes not packets"
date: 2020-07-19T10:00:00-05:00
toc: false
images:
categories:
  - tech
tags: 
  - dropped
  - qos
  - bytes
  - packets
---

Most network devices report interface drops in packets.  At some point Cisco switches running the 16.x code started reporting drops in bytes.  Reporting in bytes dramatically inflates the drop counters which makes detecting congestion issues more difficult.

Here is an example of the Discard counters reporting in bytes:

```
cisco-3850# show interfaces counters errors

Port        Align-Err     FCS-Err    Xmit-Err     Rcv-Err  UnderSize  OutDiscards
Te1/0/1             0           0           0           1          0            0
Te1/0/2             0           0           0           0          0            0
Te1/0/3             0           0           0           0          0   7569381402
Te1/0/4             0           0           0           0          0  32180592642
Te1/0/5             0           0           0           0          0            0
Te1/0/6             0           0           0           0          0    705662223
Te1/0/7             0           0           0           0          0            0
Te1/0/8             0           0           0           0          0            0
Te1/0/9             0           0           0           0          0            0
Te1/0/10            0           0           0           0          0            0
Te1/0/11            0           0           0           0          0            0
Te1/0/12            0           0           0           0          0            0
Te1/0/13            0           0           0           0          0            0
Te1/0/14            0           0           0           0          0            0
Te1/0/15            0           0           0           0          0            0
Te1/0/16            0           0           0           0          0            0
Te1/0/17            0           0           0           0          0            0
Te1/0/18            0           0           0           0          0            0
Te1/0/19            0           0           0           0          0            0
Te1/0/20            0           0           0           0          0            0
Te1/0/21            0           0           0           0          0            0
Te1/0/22            0           0           0           0          0            0
Te1/0/23            0           0           0           0          0            0
Te1/0/24            0           0           0           0          0    847069411
```

Adding this configuration statement to the switch will change reporting from bytes to packets:

```
qos queue-stats-frame-count
```


Documentation from the Cisco site:

To view the queueing statistics in packet format under show policy-map interface command output, use the qos queue-stats-frame-count command. Use the no form of this command to revert back to byte format statistics.

