---
title: "Reattaching tmux sessions from smaller client"
date: 2020-11-22T06:48:18-06:00
toc: false
images:
categories:
  - tech
tags: 
  - tmux
  - attach
---


I regularly use `mosh` and `tmux` on a phone, iPad, and laptop.  Since `mosh` does not disconnect sessions, attaching `tmux` on both screens at the same time will result in a session on the larger screen that looks like this:

![](/images/2020-11-21-20-48-38.png)

When reattaching the session on the larger screen, use the `-d` option to disconnect all other sessions first:

```
tmux attach -d
```

![](/images/2020-11-21-20-58-30.png)