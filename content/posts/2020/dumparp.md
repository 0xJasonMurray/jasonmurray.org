---
title: "Dumping ARP tables with python and Netmiko"
date: 2020-08-24T16:09:50-05:00
toc: false
images:
categories:
  - tech
tags: 
  - python
  - netmiko
  - arp
---

By using a combination of Python, Netmiko, and TextFSM, I am able to dump the current ARP table on our Cisco routers to generate a variety of diagnostic and reporting tools.   The TextFSM module to Netmiko outputs the data in JSON format for easy reporting and manipulation.  

Here is an example snippet using `pprint` to display the results:

```
#!/bin/env python3

from netmiko import Netmiko
from getpass import getpass
import pprint

password = getpass()

output = []

net_connect = Netmiko(host="192.168.0.1", username="jemurray", password=password, device_type="cisco_ios")

net_connect.find_prompt()

output.append(net_connect.send_command("show ip arp", use_textfsm=True))

for i in output:
    pp = pprint.PrettyPrinter(indent=4)
    pp.pprint(i)
```

