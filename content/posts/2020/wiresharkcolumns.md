---
title: "Adding additional data columns to Wireshark"
date: 2020-06-30T09:27:52-05:00
toc: false
images:
categories:
  - tech
tags: 
  - wireshark
---

A stock instance of Wireshark looks similar to this with frames, time, source, destination, protocol, etc as the columns:

![wireshark stock](/images/wiresharkstock.png)

To add additional columns expand out, right click the field, and select `Apply as Column`:

![wireshark add](/images/wiresharkaddascolumn.png)

For example, I have added `sequence number`, `next sequence number`, `ack number`, `bytes in flight` and `iRRT` to the default Wireshark columns:

![wireshark add](/images/wiresharkaddedcolumns.png)

Managed or edit the displayed columns by right clicking the column title bar:

![wireshark add](/images/wiresharkmanagecolumns.png)
