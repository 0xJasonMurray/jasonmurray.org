---
title: "Replying to DNS queries with Python Scapy"
date: 2020-12-16T16:15:39-06:00
toc: false
images:
categories:
  - tech
tags: 
  - scapy
  - python
  - dns
---

# Overview

Scapy is a Python program that enables users to send, sniff, dissect, and forge network packets.   In this example, I use Scapy to intercept a specific DNS query and reply with a spoofed answer.   

# Details

Start the Scapy script (scroll down to see the code):

```
(.venv) root@home-server:/home/jemurray/scapy# ./scapy-dns.py
.
Sent 1 packets.
```

Send a query to the system running the Scapy script.  For this example, there is no need to have a working DNS resolver.  The Scapy program is listening on the raw interface waiting for a DNS packet to arrive.  

The response to the query `example.com` returns the bogus answer `1.2.3.4`:

```
jemurray@Jasons-MacBook-Pro ~ % dig @192.168.86.5 example.com

; <<>> DiG 9.10.6 <<>> @192.168.86.5 example.com
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 56703
;; flags: qr aa; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;example.com.			IN	A

;; ANSWER SECTION:
example.com.		600	IN	A	1.2.3.4

;; Query time: 34 msec
;; SERVER: 192.168.86.5#53(192.168.86.5)
;; WHEN: Wed Dec 16 16:12:33 CST 2020
;; MSG SIZE  rcvd: 56
```

Packet capture of the `dig` query and Scapy response:

```
16:12:33.628252 IP 192.168.86.25.58657 > 192.168.86.5.53: 56703+ [1au] A? example.com. (40)
16:12:33.653489 IP 192.168.86.5.53 > 192.168.86.25.58657: 56703*- 1/0/0 A 1.2.3.4 (56)
```


The Scapy Python script to listen for and generate a spoofed DNS response:

```
#!/usr/bin/env python

# Import scapy libraries
from scapy.all import *

# Set the interface to listen and respond on
net_interface = "ens160"

# Berkeley Packet Filter for sniffing specific DNS packet only
packet_filter = " and ".join([
    "udp dst port 53",          # Filter UDP port 53
    "udp[10] & 0x80 = 0",       # DNS queries only
    "src host 192.168.86.25"    # IP source <ip>
    ])

# Function that replies to DNS query
def dns_reply(packet):

    # Construct the DNS packet
    # Construct the Ethernet header by looking at the sniffed packet
    eth = Ether(
        src=packet[Ether].dst,
        dst=packet[Ether].src
        )

    # Construct the IP header by looking at the sniffed packet
    ip = IP(
        src=packet[IP].dst,
        dst=packet[IP].src
        )

    # Construct the UDP header by looking at the sniffed packet
    udp = UDP(
        dport=packet[UDP].sport,
        sport=packet[UDP].dport
        )

    # Construct the DNS response by looking at the sniffed packet and manually
    dns = DNS(
        id=packet[DNS].id,
        qd=packet[DNS].qd,
        aa=1,
        rd=0,
        qr=1,
        qdcount=1,
        ancount=1,
        nscount=0,
        arcount=0,
        ar=DNSRR(
            rrname=packet[DNS].qd.qname,
            type='A',
            ttl=600,
            rdata='1.2.3.4')
        )

    # Put the full packet together
    response_packet = eth / ip / udp / dns

    # Send the DNS response
    sendp(response_packet, iface=net_interface)

# Sniff for a DNS query matching the 'packet_filter' and send a specially crafted reply
sniff(filter=packet_filter, prn=dns_reply, store=0, iface=net_interface, count=1)
``` 







