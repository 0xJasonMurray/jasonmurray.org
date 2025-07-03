---
title: "Using goaccess to analyze web server log files from the CLI"
date: 2020-05-09T17:55:43-05:00
toc: false
images:
categories:
  - tech
tags: 
  - til
  - nginx
  - logging
---

[Goaccess](https://goaccess.io/) is a real time CLI based web server log file analysis tool:

Start goaccess:

```
jemurray@shell:~$ goaccess /var/log/nginx/access.log
```

Example screeen shot:

![goaccess](/images/goaccess.png)


