---
title: "Bulk adding Infoblox host records with the infoblox_client library"
date: 2020-09-26T22:54:21-05:00
toc: false
images:
categories:
  - tech
tags: 
  - infoblox
  - infoblox-client
  - python
  - automation
---

In the technology field there are many different ways to solve a problem.  [Yesterday](/posts/2020/ansibleinfobloxaddhostrecord/), I wrote about switching from `ibcli` to `ansible` to perform mass updates of host records in Infoblox.

Today, I will solve the same problem using Python instead of Ansible.  Choosing the right tool for the job can be a daunting task.  Having a broad set of experiences and skills will make the selection process easier.


The Python script to bulk add Host records in Infoblox:

```
#!/usr/bin/env python

from infoblox_client import connector
from infoblox_client import objects


# This list could just as easily be read from an external file
host_records = [
  { 'host_name': 'host10.example.com', 'ipaddr': '127.0.0.1' },
  { 'host_name': 'host11.example.com', 'ipaddr': '127.0.0.1' },
  { 'host_name': 'host12.example.com', 'ipaddr': '127.0.0.1' }
]


opts = {'host': 'gm.example.com', 'username': 'jemurray', 'password': 'mypassword'}
conn = connector.Connector(opts)

for i in host_records:
    host_ip = objects.IP.create(ip=i['ipaddr'])
    hostRecord = objects.HostRecord.create(conn, name=i['host_name'], ip=host_ip)
    print(hostRecord)
```


Running the script:

```
(.venv) jemurray@mbp-2019:~/Documents/src $ ./createHostRecords.py
HostRecordV4: ipv4addrs="[IPv4: ipv4addr="127.0.0.1", configure_for_dhcp="False", _ref="record:host_ipv4addr/ZG5zLmhvc3RfYWRkcmVzcyQuX2RlZmF1bHQuZWR1Lnd1c3RsLm50cy5qZW11cnJheXRlc3QxMC4xMjcuMC4wLjEu:127.0.0.1/host10.example.com/default", ip="127.0.0.1", host="host10.example.com"]", name="host10.example.com", view="default", _ref="record:host/ZG5zLmhvc3QkLl9kZWZhdWx0LmVkdS53dXN0bC5udHMuamVtdXJyYXl0ZXN0MTA:host10.example.com/default", ipv4addr="127.0.0.1"

HostRecordV4: ipv4addrs="[IPv4: ipv4addr="127.0.0.1", configure_for_dhcp="False", _ref="record:host_ipv4addr/ZG5zLmhvc3RfYWRkcmVzcyQuX2RlZmF1bHQuZWR1Lnd1c3RsLm50cy5qZW11cnJheXRlc3QxMS4xMjcuMC4wLjEu:127.0.0.1/host11.example.com/default", ip="127.0.0.1", host="host11.example.com"]", name="host11.example.com", view="default", _ref="record:host/ZG5zLmhvc3QkLl9kZWZhdWx0LmVkdS53dXN0bC5udHMuamVtdXJyYXl0ZXN0MTE:host11.example.com/default", ipv4addr="127.0.0.1"

HostRecordV4: ipv4addrs="[IPv4: ipv4addr="127.0.0.1", configure_for_dhcp="False", _ref="record:host_ipv4addr/ZG5zLmhvc3RfYWRkcmVzcyQuX2RlZmF1bHQuZWR1Lnd1c3RsLm50cy5qZW11cnJheXRlc3QxMi4xMjcuMC4wLjEu:127.0.0.1/host12.example.com/default", ip="127.0.0.1", host="host12.example.com"]", name="host12.example.com", view="default", _ref="record:host/ZG5zLmhvc3QkLl9kZWZhdWx0LmVkdS53dXN0bC5udHMuamVtdXJyYXl0ZXN0MTI:host12.example.com/default", ipv4addr="127.0.0.1"
```

The resulting DNS records:

```
jemurray@mbp-2019:~ $ host host10.example.com
host10.example.com has address 127.0.0.1
```

