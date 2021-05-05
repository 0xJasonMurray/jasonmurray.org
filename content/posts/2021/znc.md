---
title: "Maintain a persistent IRC connection with ZNC (Debian), LimeChat (macOS), and Palaver (iOS)"
date: 2021-05-04T16:23:41-05:00
toc: false
images:
categories:
  - tech
tags: 
  - irc
  - znc
  - freenode
  - limechat
  - palaver
  - debian
  - linux
description: Guide for maintaining a persistent IRC connection with ZNC (Debian), LimeChat (macOS), and Palaver (iOS).
---

## Overview

Prior to Slack, Teams, iMessage, Discord, WeChat, Signal, and the [plethora](https://en.wikipedia.org/wiki/Messaging_apps) of communication platforms, we had [IRC](https://en.wikipedia.org/wiki/Internet_Relay_Chat). Unlike todays crop of chat networks, [IRC is built on top of an open communication protocol](https://tools.ietf.org/html/rfc1459). These open standards facilitate innovation by allowing developers to design [clients](https://en.wikipedia.org/wiki/Comparison_of_Internet_Relay_Chat_clients) to meet the needs of their users, move data between providers, and gain a deeper understanding of data management, without being encumbered by proprietary platforms.

IRC is a real time communication platform, it struggles with persistent chat history or seemless support of stateful simultaneous clients (ie. mobile and desktop). I would be amiss to ignore over this glaring gap in functionality many of the more predominate clients of today offer. IRC is still under [active development](https://ircv3.net/wg). Enhancements under the  [IRCv3](https://ircv3.net/irc/) working group are adding features like [chat history](https://ircv3.net/specs/extensions/chathistory) and [server time](https://ircv3.net/specs/extensions/server-time) to facilitate more robust persistence and history features.

Until these additional features become widely adopted, we can work around the gaps with an [IRC bouncer](https://en.wikipedia.org/wiki/BNC_(software)) like [ZNC](https://wiki.znc.in/ZNC). A bouncer runs on a system with a constant connection to the IRC server. Instead of the IRC server maintaining a client’s chat history, the bouncer acts as a proxy, keeping track of when the client is away and replaying all messages when they return.

[![Image of ](/images/2021-05-05-14-39-06.png)](/images/2021-05-05-14-39-06.png)

## Details

In this post I detail configuring and using ZNC to maintain a persistent IRC connection to FreeNode. The "always on" server is a $5 per/month Debian [virtual private server running at Digital Ocean](https://jasonmurray.org/posts/2020/homelab/#saas-services).

### ZNC on Debian

Install `znc` on Debian:

```text
apt install znc
```

Generate the initial configuration:

```text
znc --makeconf
```

Output from `znc --makeconf`:

```
jemurray@shell:~$ znc --makeconf
[ .. ] Checking for list of available modules...
[ ** ]
[ ** ] -- Global settings --
[ ** ]
[ ?? ] Listen on port (1025 to 65534): 64697
[ ?? ] Listen using SSL (yes/no) [no]: yes
[ ?? ] Listen using both IPv4 and IPv6 (yes/no) [yes]: yes
[ .. ] Verifying the listener...
[ ** ] Unable to locate pem file: [/home/jemurray/.znc/znc.pem], creating it
[ .. ] Writing Pem file [/home/jemurray/.znc/znc.pem]...
[ ** ] Enabled global modules [webadmin]
[ ** ]
[ ** ] -- Admin user settings --
[ ** ]
[ ?? ] Username (alphanumeric): jemurray
[ ?? ] Enter password:
[ ?? ] Confirm password:
[ ?? ] Nick [jemurray]:
[ ?? ] Alternate nick [jemurray_]:
[ ?? ] Ident [jemurray]:
[ ?? ] Real name (optional): Jason Murray
[ ?? ] Bind host (optional):
[ ** ] Enabled user modules [chansaver, controlpanel]
[ ** ]
[ ?? ] Set up a network? (yes/no) [yes]:
[ ** ]
[ ** ] -- Network settings --
[ ** ]
[ ?? ] Name [freenode]:
[ ?? ] Server host [chat.freenode.net]:
[ ?? ] Server uses SSL? (yes/no) [yes]:
[ ?? ] Server port (1 to 65535) [6697]:
[ ?? ] Server password (probably empty):
[ ?? ] Initial channels: 0x29
[ ** ] Enabled network modules [simple_away]
[ ** ]
[ .. ] Writing config [/home/jemurray/.znc/configs/znc.conf]...
[ ** ]
[ ** ] To connect to this ZNC you need to connect to it as your IRC server
[ ** ] using the port that you supplied.  You have to supply your login info
[ ** ] as the IRC server password like this: user/network:pass.
[ ** ]
[ ** ] Try something like this in your IRC client...
[ ** ] /server <znc_server_ip> +64697 jemurray:<pass>
[ ** ]
[ ** ] To manage settings, users and networks, point your web browser to
[ ** ] https://<znc_server_ip>:64697/
[ ** ]
[ ?? ] Launch ZNC now? (yes/no) [yes]: yes
[ .. ] Opening config [/home/jemurray/.znc/configs/znc.conf]...
[ .. ] Loading global module [webadmin]...
[ .. ] Binding to port [+64697]...
[ ** ] Loading user [jemurray]
[ ** ] Loading network [freenode]
[ .. ] Loading network module [simple_away]...
[ >> ] [/usr/lib/znc/simple_away.so]
[ .. ] Adding server [chat.freenode.net +6697 ]...
[ .. ] Loading user module [chansaver]...
[ .. ] Loading user module [controlpanel]...
[ .. ] Forking into the background...
[ >> ] [pid: 21576]
[ ** ] ZNC 1.7.2+deb3 - https://znc.in
```

To configure `znc` through a web browser, connect to `https://shell.jasonmurray.org:64697/`:

[![Image of ](/images/2021-04-27-16-34-00.png)](/images/2021-04-27-16-34-00.png)

Under `Your Settings -> Networks (freenode) -> Edit`

Enable the `nickserv` module to save your Nick:

[![Image of ](/images/2021-04-27-16-56-37.png)](/images/2021-04-27-16-56-37.png)

Enable the `route_replies` modules to deal with multiple clients:

[![Image of ](/images/2021-04-27-20-57-39.png)](/images/2021-04-27-20-57-39.png)

Enable the `logging` module:

[![Image of ](/images/2021-04-27-21-35-06.png)](/images/2021-04-27-21-35-06.png)

## LimeChat Configuration

On the macOS desktop, I use [LimeChat](http://limechat.net/mac/) as my IRC client.

Add a new server in `LimeChat`:

[![Image of ](/images/2021-04-27-16-36-49.png)](/images/2021-04-27-16-36-49.png)

Configure LimeChat to use the ZNC proxy:

- Network Name: `Use a unique name`
- Server: `DNS name of the server ZNC is running on`
- Port: `The port ZNC is configured to listen on`
- SSL: `Enable`
- Server Password: `Password configured for ZNC user`
- Nickname: `Name visable to IRC users`
- Login name: `ZNC-user-name/znc-network-name-configured-above`
- Real Name: `Real Name`
- Nickserv Password: `Not needed, configure NicServ module in the ZNC server`

[![Image of ](/images/2021-04-27-16-40-30.png)](/images/2021-04-27-16-40-30.png)


LimeChat IRC sessions are proxied through the ZNC server:

[![Image of ](/images/2021-04-27-16-45-40.png)](/images/2021-04-27-16-45-40.png)

## Palaver

On the iPhone, I use [Palaver](https://palaverapp.com/) as my IRC client.


Add the ZNC network configuration:

- Name: `Use a unique name`
- Address: `DNS name of the server ZNC is running on`
- Port: `The port ZNC is configured to listen on`
- SSL: `True`
- Authentication: `ZNC`

[![Image of ](/images/IMG_2094.PNG)](/images/IMG_2094.PNG)

Configure ZNC authentication:

- Username: `ZNC user name`
- Network: `Network name configured in initial ZNC setup`
- Password: `your password`

[![Image of ](/images/IMG_2096.PNG)](/images/IMG_2096.PNG)


Accept the certificate from the ZNC server:

[![Image of ](/images/IMG_2095.PNG)](/images/IMG_2095.PNG)


Successful connection:

[![Image of ](/images/IMG_2097.PNG)](/images/IMG_2097.PNG)