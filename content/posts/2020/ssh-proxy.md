---
title: "Using SSH as a SOCKS proxy"
date: 2020-04-18T13:41:14-05:00
categories:
  - tech
tags: [til,ssh,socks5]
---
SSH can function as a SOCKS proxy with the following command:

```
ssh -D 5555 -q -C -N shell.jasonmurray.org
```

In the following examples, I used [SwitchyOmega](https://chrome.google.com/webstore/detail/proxy-switchyomega/padekgcemlokbadohgkifijomclgjgif?hl=en) as the plugin to change the proxy settings.

Configure the browser with the proxy:

![Chrome Proxy](/images/socksproxy.png)

AutoProxy is enabled for specific sites, otherwise bypass the proxy and fetch directly:

![Chrome SwitchyOmega AutoProxy](/images/autoproxy.png)
