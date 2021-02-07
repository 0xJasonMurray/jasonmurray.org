---
title: "BGP authentication requires MD5 hash in TCP header"
date: 2020-08-03T13:25:39-05:00
toc: false
images:
categories:
  - tech
tags: 
  - md5
  - vmx
  - bgp
---

When BGP authentication is configured, a connecting client passes the MD5 hash in the TCP option header during the initial SYN packet.

Using the following configuration, it is impossible to connect to TCP port 179 without the MD5 key in the header:

```
jemurray@eps-l29-LAB# show protocols bgp group TEST
type external;
neighbor 10.1.1.2 {
    authentication-key "$9$WAX8-woaUH.5GD/Cpu1I-Vb"; ## SECRET-DATA
    peer-as 174;
}
```

When a connection is made without the MD5 key, the remote device rejects the connection as if the port is not listening:

```
ubuntu@bgp-test:~$ telnet 10.1.1.1 179
Trying 10.1.1.1...
telnet: Unable to connect to remote host: Connection timed out
```

Disable MD5 authentication and test again:

```
jemurray@eps-l29-LAB# deactivate protocols bgp group TEST neighbor 10.1.1.2 authentication-key


jemurray@eps-l29-LAB# show protocols bgp group TEST
type external;
neighbor 10.1.1.2 {
    inactive: authentication-key "$9$WAX8-woaUH.5GD/Cpu1I-Vb"; ## SECRET-DATA
    peer-as 174;
}
```

Connections to port 179 are allowed without authentication:

```
ubuntu@bgp-test:~$ telnet 10.1.1.1 179
Trying 10.1.1.1...
Connected to 10.1.1.1.
Escape character is '^]'.
```

The connection failure is logged:

```
Aug  4 12:37:56  eps-l29-LAB kernel: tcp_auth_ok: Packet from 10.1.1.2:41412 missing MD5 digest
```

Looking at the TCP headers of a successful connection, we can see the MD5 header option in a packet capture:

```
Transmission Control Protocol, Src Port: 65113, Dst Port: 179, Seq: 0, Len: 0
    Source Port: 65113
    Destination Port: 179
    Options: (40 bytes), Maximum segment size, No-Operation (NOP), Window scale, No-Operation (NOP), No-Operation (NOP), Timestamps, No-Operation (NOP), No-Operation (NOP), TCP MD5 signature
        TCP Option - Maximum segment size: 1440 bytes
        TCP Option - No-Operation (NOP)
        TCP Option - Window scale: 0 (multiply by 1)
        TCP Option - No-Operation (NOP)
        TCP Option - No-Operation (NOP)
        TCP Option - Timestamps: TSval 2321717372, TSecr 0
        TCP Option - No-Operation (NOP)
        TCP Option - No-Operation (NOP)
        TCP Option - TCP MD5 signature
            Kind: MD5 Signature Option (19)
            Length: 18
            MD5 digest: e1672b02516ee303dcd5832e529aa763
    [Timestamps]
```

More information can be found in [RFC 2385](https://tools.ietf.org/html/rfc2385).