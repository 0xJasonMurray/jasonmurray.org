---
title: "Mosh (mobile shell) as a replacement for traditional SSH sessions"
date: 2020-05-01T15:32:03-05:00
categories:
  - tech
tags: [til,ssh, mosh]
---

[Mosh](https://mosh.org/) is used as a replacement for interactive SSH sessions.   It converts the standard ssh session from TCP to UDP, allowing for stateless roaming, changing IP address in an active remote session, and resuming a session after traveling from work to home.  It's more robust and responsive, especially over Wi-Fi, cellular, and long-distance links.

Install on a MAC: `brew install mosh`

![Mosh Terminal](/images/mosh.gif)


For iOS (iPad, iPhone), I use [Blink Shell](https://blink.sh/) with built-in Mosh support. This works well when traveling light and connected to a cellular hotspot.  No need to worry about spotty signals or dropped connections.  Mosh will automatically reconnect.

![Blink Shell](/images/blinkshell.jpg)


Mosh is available is most Linux distributions: `sudo apt install mosh`
