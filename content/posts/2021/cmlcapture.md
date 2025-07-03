---
title: "Link packet capture in Cisco Modeling Lab"
date: 2021-04-13T15:46:54-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cml
  - pcap
  - wireshark
  - networking
description:
---

## Overview

To inspect network traffic on the wire, Cisco Modeling Lab provides a native packet capture interface. This feature enables packet analysis without configuring the more complicated span port and capture device.

## Details

Click the router link, then `start`, to enable a packet capture:

[![Image of ](/images/2021-04-13-15-49-01.png)](/images/2021-04-13-15-49-01.png)


A spinning box and `Waiting for packets...` appears:

[![Image of ](/images/2021-04-13-15-58-07.png)](/images/2021-04-13-15-58-07.png)

Packets are displayed in real time as they pass through the link:

[![Image of ](/images/2021-04-13-15-58-37.png)](/images/2021-04-13-15-58-37.png)

Click `Download` to retrieve a `pcap` file on the local system:

[![Image of ](/images/2021-04-13-16-01-01.png)](/images/2021-04-13-16-01-01.png)

Open the `pcap` in a tool such as `wireshark`:

[![Image of ](/images/2021-04-13-16-02-28.png)](/images/2021-04-13-16-02-28.png)