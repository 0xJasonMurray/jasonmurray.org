---
title: "Restarting the IS-IS process on Cisco NX-OS"
date: 2020-04-25T17:57:54-05:00
categories:
  - tech
tags: [til, cisco, is-is, ldp, mpls]
---

One of our Cisco Nexus 7K's ran into a problem where LDP would not establish neighbors and IS-IS was not advertising routes:

```
nx7k-MPLS# show mpls ldp neighbor
nx7k-MPLS#
```

```
nx7k-MPLS# show isis database level-2 ip prefix 192.168.247.154/32   ! This is the lo0 interface IP address
IS-IS Process: isis-config LSP database VRF: default
IS-IS Level-2 Link State Database
  LSPID                 Seq Number   Checksum  Lifetime   A/P/O/T

nx7k-MPLS#
```

The router hit a [bug](https://bst.cloudapps.cisco.com/bugsearch/bug/CSCud28261/?rfs=iqvred), but restarting IS-IS resolved the issue:

```
nx7k-MPLS# restart isis isis-config
```

The restart resolved the issue: 
```
nx7k-MPLS# show mpls ldp neighbor
    Peer LDP Ident: 192.168.247.26:0; Local LDP Ident 192.168.247.154:0
        TCP connection: 192.168.247.26.646 - 192.168.247.154.58383
        State: Oper; Msgs sent/rcvd: 39/114; Downstream
        Up time: 00:00:21
        LDP discovery sources:
          Ethernet7/19, Src IP addr: 192.168.161.182
        Addresses bound to peer LDP Ident:
          192.168.247.26  192.168.161.182 192.168.161.180
```
