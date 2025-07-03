---
title: "pSConfig template error"
date: 2020-09-02T12:50:41-05:00
draft: true
toc: false
images:
categories:
  - tech
tags: 
  - perfsonar
  - psconfig
---

Ran into the following error on fresh install of perfSonar 4.2.4-CentOS7 when attempting to save scheduled tests:

```
 Error - Couldn't format pSConfig template file
```

After running and system update and rebooting:

```
sudo yum update && reboot
```

The problem went away.  