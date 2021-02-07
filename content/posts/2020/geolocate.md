---
title: "Geolocating any IP address in a text file with Python"
date: 2020-04-28T20:48:14-05:00
categories:
  - tech
tags: [til, python, maxmind, geolocate]
---

A Python script that will geolocate any IP address found in a text file.

The Python script:

```
#!/usr/local/bin/python3

# Uses the IP database: https://dev.maxmind.com/geoip/geoip2/downloadable/#MaxMind_APIs

import geoip2.database
import fileinput
import re

reader = geoip2.database.Reader('~/g/scripts/wustlPython/GeoLite2-City/GeoLite2-City.mmdb')

for line in fileinput.input():
    # Match any IPv4 address found
    ip = re.findall( r'[0-9]+(?:\.[0-9]+){3}', line )
    if ip:
        #print(ip)
        for i in ip:
            try:
                response = reader.city(i)
                country = response.country.name

                try:
                    state = response.subdivisions.most_specific.name
                except:
                    state = "none"

                try:
                    city = response.city.name
                except:
                    city = "none"

                print("IP: %s, Country: %s, State: %s, City: %s" % (i, country, city, state))

            except:
                next
```

Example input:

```
jemurray@mbp-2019:~/g/scripts/wustlPython $ head -20 ips2.txt

52.94.232.208
Country
United States
DNS Name
dp-1.storagegateway.us-east-1.amazonaws.com
  52.94.232.208
932,316,573,638
924,432,432,731
7,884,140,907
149
  176.32.103.253
574,130,284,981
570,833,661,079
3,296,623,902
127
  54.239.18.32
554,068,088,708
550,432,749,625
3,635,339,083
```

Example run:

```
jemurray@mbp-2019:~/g/scripts/wustlPython $ head -20 ips2.txt | ./geoIp4.py
IP: 52.94.232.208, Country: United States, State: Ashburn, City: Virginia
IP: 52.94.232.208, Country: United States, State: Ashburn, City: Virginia
IP: 176.32.103.253, Country: United States, State: Ashburn, City: Virginia
IP: 54.239.18.32, Country: United States, State: Ashburn, City: Virginia
```
