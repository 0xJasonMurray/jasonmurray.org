---
title: "Persistent SSH tunnel with autossh"
date: 2020-11-19T15:51:41-06:00
toc: false
images:
categories:
  - tech
tags: 
  - autossh
  - ssh
---

# Overview

To access websites behind an internal network, I proxy web traffic to specific DNS domains over an `ssh` connection using the SOCKS protocol.  The SwitchyOmega plugin takes care of site specific proxying and `autossh` securely forwards all SOCKS traffic through the `ssh` tunnel.  

To handle network instability, VPN connectivity, and laptop mobility, `autossh` automatically restarts the `ssh` tunnel during network changes or failures.

# Details

## Prerequisite 

`ssh` [key authentication](/posts/2020/sshkeygen/) must be setup prior to using `autossh`.  The `ssh` session can not prompt for a password.

## Tunnel Setup

Start the `ssh` tunnel using `autossh`:

```
autossh -f -D 5555 -N -M 0 host.example.com
```

## Testing

Test the connection:

```
jemurray@mbp-2019:~ $ telnet localhost 5555
Trying ::1...
Connected to localhost.
Escape character is '^]'.
quit
Connection closed by foreign host.
```

Kill the `ssh` test on the remote site for testing:

```
# Look for the process without a pty session associated with it
jemurray@host:~$ ps -ef | grep ssh | grep jemurray
jemurray 18053 17885  0 15:49 ?        00:00:00 sshd: jemurray@pts/10
jemurray 20287 20119  0 15:53 ?        00:00:00 sshd: jemurray

# kill the process
jemurray@host:~$ kill 20287
```

The session dies, but automatically restarts:

```
emurray@mbp-2019:~ $ telnet localhost 5555
Trying ::1...
Connected to localhost.
Escape character is '^]'.
quit
Connection closed by foreign host.
```
