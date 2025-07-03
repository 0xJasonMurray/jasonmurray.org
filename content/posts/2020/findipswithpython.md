---
title: "Using Python to find IP addresses in a text file"
date: 2020-06-27T16:45:50-05:00
toc: false
images:
categories:
  - tech
tags: 
  - python
  - regex
---

Given a text file with IP address randomly placed within it:

```
jemurray@mbp-2019:~/Documents/scripts/wustlPython $ cat ipsampleinput.txt
This is a text file with ip address: 192.168.1.1 within it, there are more addresses like this one 10.23.12.1
sprinkled throughout the 1.2.3.4 file.  SomeRight10.3.3.2Next to each other some spaced apart.
```

The following Python script will extract all IP address within the file:

```
#!/usr/local/bin/python3

import fileinput
import re

for line in fileinput.input():
    ip = re.findall( r'[0-9]+(?:\.[0-9]+){3}', line )
    if ip:
        for i in ip:
            print(i)
```

Output:

```
jemurray@mbp-2019:~/Documents/scripts/wustlPython $ cat ipsampleinput.txt | ./findAllIpAddresses.py
192.168.1.1
10.23.12.1
1.2.3.4
10.3.3.2
```

