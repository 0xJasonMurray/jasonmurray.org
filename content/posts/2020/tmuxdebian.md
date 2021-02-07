---
title: "Mouse mode with tmux in iTerm2"
date: 2020-07-21T12:50:03-05:00
toc: false
images:
categories:
  - tech
tags: 
  - tmux
  - debian
---

I was troubleshooting an issue where `mouse mode` stopped working while using iTerm2 on macOS.  The iTerm2 option `mouse reporting` must be enabled for `mouse mode` to function properly:


![mouse reporting](/images/enablemousereporting.png)


Next validate, `mouse mode` is configured in the `~/.tmux.conf` file, with the following command:

```
set -g mouse on
```

Verify `mouse mode` is working by starting `tmux` and running: `CTRL+B :show -g`, the following option should appear in the output:

```
...
mouse on
...
```

Select some text or scroll back while in `tmux`.  The scroll back buffer in the top right should appear and highlighted text will be yellow:

![tmux](/images/mousemodehelloworld.png)


To temporarily bypass `mouse mode`, hold down the `option` key and select text:

![tmux](/images/copymodehelloworld.png)

