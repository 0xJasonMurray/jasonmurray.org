---
title: "Using Python TextFSM to retrieve structured output from Cisco devices"
date: 2020-05-05T16:40:31-05:00
categories:
  - tech
tags: [til, python, textfsm, cisco, networking, automation]
---

[Cisco's](https://www.cisco.com/) lack of a robust API or the ability to return structured output from the CLI is a challenge when trying to automate networking configuration or retrieve telemetry information.

The Python module [TextFSM](https://github.com/google/textfsm) attempts to resolve this deficiency by executing commands via the CLI and returning structured JSON formatted output.

Example Python script:

```
#!/usr/local/bin/python3

from netmiko import Netmiko
from getpass import getpass
import pprint

password = getpass()

output = []

net_connect = Netmiko(host="192.168.1.1", username="jemurray", password=password, device_type="cisco_ios")

net_connect.find_prompt()

output.append(net_connect.send_command("show version", use_textfsm=True))
output.append(net_connect.send_command("show interface GigabitEthernet1/0/1", use_textfsm=True))
output.append(net_connect.send_command("show interface GigabitEthernet1/0/12", use_textfsm=True))
output.append(net_connect.send_command("show vlan id 120", use_textfsm=True))


for i in output:
    pp = pprint.PrettyPrinter(indent=4)
    pp.pprint(i)
```

Returned output:

```
jemurray@mbp-2019:~/g/scripts $ ./structuredSwitchOuput.py
Password:
[   {   'config_register': '0x102',
        'hardware': ['WS-C3850-12S'],
        'hostname': 'LAB-3850-SW',
        'mac': ['00:56:2b:13:aa:bb'],
        'reload_reason': 'Power Failure or Unknown',
        'rommon': 'IOS-XE',
        'running_image': 'packages.conf',
        'serial': ['LABSERIALNUMBER'],
        'uptime': '1 year, 35 weeks, 5 days, 8 hours, 53 minutes',
        'version': '16.6.4'}]

[   {   'address': '0056.2b13.fc81',
        'bandwidth': '1000000 Kbit',
        'bia': '0056.2b13.fc81',
        'delay': '10 usec',
        'description': 'rd-51-wu-ml-0 Ten1/0/7  MANAGED',
        'duplex': 'Full-duplex',
        'encapsulation': 'ARPA',
        'hardware_type': 'Gigabit Ethernet',
        'input_errors': '0',
        'input_packets': '81953194',
        'input_rate': '1000',
        'interface': 'GigabitEthernet1/0/1',
        'ip_address': '',
        'last_input': '00:00:03',
        'last_output': '00:00:01',
        'last_output_hang': 'never',
        'link_status': 'up',
        'mtu': '1500',
        'output_errors': '0',
        'output_packets': '96787774',
        'output_rate': '7000',
        'protocol_status': 'up (connected)',
        'queue_strategy': 'fifo',
        'speed': '1000Mb/s'}]

[   {   'address': '0056.2b13.fc8c',
        'bandwidth': '1000000 Kbit',
        'bia': '0056.2b13.fc8c',
        'delay': '10 usec',
        'description': 'des BUILD NETWORK MANAGED',
        'duplex': 'Full-duplex',
        'encapsulation': 'ARPA',
        'hardware_type': 'Gigabit Ethernet',
        'input_errors': '0',
        'input_packets': '17238614',
        'input_rate': '0',
        'interface': 'GigabitEthernet1/0/12',
        'ip_address': '',
        'last_input': '1y32w',
        'last_output': '00:00:00',
        'last_output_hang': 'never',
        'link_status': 'up',
        'mtu': '1500',
        'output_errors': '0',
        'output_packets': '38493915',
        'output_rate': '0',
        'protocol_status': 'up (connected)',
        'queue_strategy': 'fifo',
        'speed': '1000Mb/s'}]

[   {   'interfaces': ['Gi1/0/1'],
        'name': 'VLAN0120',
        'status': 'active',
        'vlan_id': '120'}]
```


Git repo and more information [here](https://github.com/google/textfsm)
