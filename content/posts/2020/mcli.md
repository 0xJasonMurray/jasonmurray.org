---
title: "m is the CLI for macOS"
date: 2020-06-01T08:34:31-05:00
toc: false
images:
categories:
  - tech
tags: 
  - mcli
  - macos
  - cli
---

Not all tasks are easy to perform with a GUI, the CLI opens up a new realm of possibilities with scripting, automation, and simplified work flows.  

[`m` is a 3rd party tool for interacting with macOS via the CLI.](https://github.com/rgcr/m-cli)

Install `m`:

```
brew install m-cli
```

Example command with `m`:

```
jemurray@mbp-2019:~ $ sudo m firewall status
Firewall is enabled. (State = 1)
```

The full command set:

```
jemurray@mbp-2019:~ $ m

  Swiss Army Knife for macOS ! 


usage:  m [OPTIONS] COMMAND [help]

    OPTIONS
        --update        update m-cli to the latest version
        --uninstall     uninstall m-cli

    COMMANDS:
        help
        battery
        bluetooth
        dir
        disk
        display
        dns
        dock
        finder
        firewall
        flightmode
        gatekeeper
        group
        hostname
        info
        itunes
        lock
        network
        nosleep
        notification
        ntp
        printer
        restart
        safeboot
        screensaver
        service
        shutdown
        sleep
        timezone
        trash
        update
        user
        volume
        vpn
        wallpaper
        wifi
```

As an example, I was able to use `m` to automate desktop builds using [Ansible](/posts/2020/ansiblelaptop/).  Having the right tool for the job is invaluable.  
