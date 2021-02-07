---
title: "Finding configuration lines on Cisco hardware with Python"
date: 2020-07-14T10:13:14-05:00
toc: false
images:
categories:
  - tech
tags: 
  - python
  - textfsm
  - automation
---

I love simple automation and scripting.  

In this example, we had to audit thousands of network devices for a specific configuration line.  Doing this by hand would take hours, with Python it takes minutes.   (Yes, we will automate the fix at some point).

Basic conditions and loops combined with a handful of network libraries and regular expression knowledge can go a long way to making a more productive day.


```
#!/usr/local/bin/python3

from netmiko import Netmiko
from getpass import getpass

password = getpass()

input = open('washu-3850-inventory.txt', 'r')

for i in input:
    output = []
    devices = i.split()

    if devices[0]:
        try:
            net_connect = Netmiko(host=devices[1], username="myusername", password=password, device_type="cisco_ios")
            net_connect.find_prompt()
        except:
            next

        output.append(net_connect.send_command("show run | inc frame", use_textfsm=True))

        if output[0]:
            print("Found on " + devices[0] + " " + output[0])
        else:
            print("Not found on " + devices[0])
```