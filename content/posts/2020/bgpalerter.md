---
title: "Monitor for BGP changes using BGPalerter"
date: 2020-04-20T17:10:20-05:00
categories:
  - tech
tags: [til, bgp]
---

[BGPalerter](https://github.com/nttgin/BGPalerter) is a free and opensource way to monitor for changes in public BGP announcments.   It is an alternative to [BGPmon](https://bgpmon.net/).


Initial Setup (first run):

```
jemurray@shell:~$ ./bgpalerter-linux-x64
Impossible to load config.yml. A default configuration file has been generated.
BGPalerter, version: 1.24.0 environment: production
Loaded config: /home/jemurray/config.yml
? The file prefixes.yml cannot be loaded. Do you want to auto-configure BGPalerter? Yes
? Which Autonomous System(s) you want to monitor? (comma-separated, e.g. 2914,3333) 2552
? Are there sub-prefixes delegated to other ASes? (e.g. sub-prefixes announced by customers) No
? Do you want to be notified when your AS is announcing a new prefix? Yes
Generating monitoring rule for 192.31.46.0/24
Generating monitoring rule for 128.252.7.0/24
Generating monitoring rule for 2606:fd80::/32
Generating monitoring rule for 2604:3e00::/32
Generating monitoring rule for 128.252.25.0/24
Generating monitoring rule for 128.252.0.0/16
Generating monitoring rule for 128.252.48.0/24
Generating monitoring rule for 65.254.96.0/19
Generating monitoring rule for 65.254.109.0/24
Detected more specific 128.252.7.0/24
Detected more specific 128.252.25.0/24
Detected more specific 128.252.48.0/24
Detected more specific 65.254.109.0/24
Generating generic monitoring rule for AS 2552
WARNING: The generated configuration is a snapshot of what is currently announced. Some of the prefixes don't have ROA objects associated or are RPKI invalid. Please, verify the config file by hand!
Done!
Monitoring 128.252.0.0/16
Monitoring 65.254.96.0/19
Monitoring 192.31.46.0/24
Monitoring 2604:3e00::/32
Monitoring 2606:fd80::/32
Monitoring AS 2552
```


Restarting:

```
jemurray@shell:~$ ./bgpalerter-linux-x64
BGPalerter, version: 1.24.0 environment: production
Loaded config: /home/jemurray/config.yml
Monitoring 128.252.0.0/16
Monitoring 65.254.96.0/19
Monitoring 192.31.46.0/24
Monitoring 2604:3e00::/32
Monitoring 2606:fd80::/32
Monitoring AS 2552
```


