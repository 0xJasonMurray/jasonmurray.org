---
title: "Regex to find MAC addresses with random bit set"
date: 2020-08-06T16:55:08-05:00
toc: false
images:
categories:
  - tech
tags: 
  - python
  - regex
  - randommacaddress
---

Apple has brought to light a new feature they are enabling in iOS14 called [private wi-fi addressing](https://support.apple.com/en-us/HT211227).  Before a device connects to the network the MAC address will be randomized.  This feature has the potential to wreak havoc on many enterprise systems that have historically treated the MAC address as a unique identifier.  Captive portals, client reporting, mobile device managers, and a variety of wireless authentication systems are examples of systems that may rely on the uniqueness of the MAC address.   While I support what Apple is trying to do, changing the default behavior with little notice is not the best way to keep our infrastructure engineers happy.

Encoded within the MAC address are bits to indicate if the address is [unique or dynamically generated](https://en.wikipedia.org/wiki/MAC_address#Universal_vs._local).  

Using this information, I wrote a python script to find any locally generated MAC address and print out the first 16 bits known as the organizationally unique identifier (OUI).

```
#!/usr/bin/env python

import fileinput
import re

for line in fileinput.input():
    ip = re.findall( r'(([0-9A-Fa-f]{1}[26aeAE]{1}[:-]([0-9A-Fa-f]{2}[:-]){2})([0-9A-Fa-f]{2}[:-]){2}[0-9A-Fa-f]{2})', line )
    if ip:
        for i in ip:
            print(i[1])
```

I was curious if we have any of these devices on our network yet.  Parsing the DHCP logs we find:

```
[jemurray@loghost infoblox]$ cat infoblox.log | ~/findAllDynamicMacAddresses.py | sort | uniq -c | sort -rn | head -5
    876 52:41:53:
    856 f2:3d:00:
    472 42:df:d0:
    431 06:b0:00:
    352 82:c7:4b:
```

I can't say enough good things about [RegEx101](https://regex101.com/).  This site is invaluable for creating and debugging regular expressions:

![regex101](/images/regex101randmac.png)