---
title: "Moving configuration with the Juniper rename command"
date: 2020-07-10T13:23:15-05:00
toc: false
images:
categories:
  - tech
tags: 
  - juniper
  - rename
---

The Juniper platform has a feature which allows configuration sections to be moved (renamed) with the `rename` command.  For example, moving a port configuration from one interface to another.

Original configuration:

```
[edit]
root@900w# show interfaces
ge-0/0/2 {
    unit 0 {
        family inet {
            address 192.168.0.1/24;
        }
    }
}
```

Moving the configuration:

```
[edit]
root@900w# rename interfaces ge-0/0/2 to ge-0/0/5
```

Validating the move:

```
[edit]
root@900w# show interfaces
ge-0/0/5 {
    unit 0 {
        family inet {
            address 192.168.0.1/24;
        }
    }
}
```