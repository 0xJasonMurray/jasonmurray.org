---
title: "How to fix automatic indenting when pasting into vim"
date: 2020-12-29T14:56:08-06:00
toc: false
images:
categories:
  - tech
tags: 
  - vim
  - paste
---

# Overview

When pasting text into a `vim` editor session, we occasionally run into an issue where line spacing is incorrect due to the `autoindent` or `smartindent` feature.  When either of these options are set, `vim` tries to properly indent the lines based on the file type.   However, if the original source is already indented, `smartindent` doubles the indentation.

# Details

When the following options are configured in `vim` (`autoindent` and `smartindent`):

```
:set
--- Options ---
  autoindent          background=dark     helplang=en         modified            scroll=26           smartindent         ttyfast             ttymouse=xterm      window=0
  fileencoding=utf-8
  fileencodings=ucs-bom,utf-8,default,latin1
  iskeyword=@,48-57,_,192-255
```

Pasting a Python program with existing indentation looks like this:

```
#!/usr/bin/env python
from scapy.all import *
net_interface = "lo0"
# SNIFF filter
packet_filter = " and ".join([
        "udp dst port 53",
            "udp[10] & 0x80 = 0",
                "src host 127.0.0.1"
                    ])
def dns_reply(packet):
        # Construct the DNS packet
            eth = Ether()
                ip = IP(
                                src=packet[IP].dst,
                                        dst=packet[IP].src
                                                )
                    udp = UDP(
                                    dport=packet[UDP].sport,
                                            sport=packet[UDP].dport
                                                    )
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
-- INSERT                                      29,54         All
```

To temporarily disable `smartindent` while pasting text, execute the follow option while in `command` mode:

```
:set paste
```

When the `set paste` option is enabled, pasting a Python program in `vim` looks like this:

```
#!/usr/bin/env python
from scapy.all import *
net_interface = "lo0"
# SNIFF filter
packet_filter = " and ".join([
    "udp dst port 53",
    "udp[10] & 0x80 = 0",
    "src host 127.0.0.1"
    ])
def dns_reply(packet):
    # Construct the DNS packet
    eth = Ether()
    ip = IP(
        src=packet[IP].dst,
        dst=packet[IP].src
        )
    udp = UDP(
        dport=packet[UDP].sport,
        sport=packet[UDP].dport
        )
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~                     
~               
-- INSERT (paste) --                                                                         
```

To disable `set paste`, type:

```
:set nopaste
```