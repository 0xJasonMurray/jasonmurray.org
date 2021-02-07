---
title: "SSH Escape Sequence"
date: 2021-01-16T14:35:06-06:00
toc: false
images:
categories:
  - tech
tags: 
  - ssh
  - escapesequence
---

# Overview

An `escape sequence` is a set of characters which signals to the application, another action will be taken.  For example, sending the `break sequence` to a serial console, or the `hangup / terminate` sequence to a stuck `ssh` session.

To access the `escape sequence` in a terminal based `ssh` session use the `~` followed by the option.  For example, `~?` displays the `escape sequence menu` within SSH.

# Details

Connect to a server through `ssh`:

```
jemurray@jasons-mbp:~ $ ssh -A shell.jasonmurray.org
Autopushing login request to phone...
Success. Logging you in...
jemurray@shell:~$
```

Press `~?` to bring up the `escape sequence menu`:

```
jemurray@shell:~$ ~?
Supported escape sequences:
 ~.   - terminate connection (and any multiplexed sessions)
 ~B   - send a BREAK to the remote system
 ~C   - open a command line
 ~R   - request rekey
 ~V/v - decrease/increase verbosity (LogLevel)
 ~^Z  - suspend ssh
 ~#   - list forwarded connections
 ~&   - background ssh (when waiting for connections to terminate)
 ~?   - this message
 ~~   - send the escape character by typing it twice
(Note that escapes are only recognized immediately after newline.)
```

The most common reason to access the escape menu is to `send break` to a serial terminal server or `terminate` a hung `ssh` session.  

To send break, enter: `~B`.  I don't have a serial console server in my home lab to demo this option.  If you are reading this I suspect you know what it does.   


What happens if you have to jump through two SSH hosts and need to access the `escape menu` on the second host?  `ssh` has an option to send an `escape sequence` to the `escape sequence` by entering two `~`'s.

If we log into 2 hosts, one after another, like this: `jasons-mbp -> shell -> sdf`; The `escape sequence menu` on the SDF server, must be accessed by entering 2 `~` characters.


For example, after logging into SDF, I need to end a hung `ssh` session.   By pressing `~~.` (2 tildes and a dot), the hung `ssh` session will disconnect from SDF only.

```
jemurray@shell:~$
jemurray@shell:~$ ssh sdf.org
jemurray@sdf:~ $
jemurray@sdf:~ $ Connection to sdf.org closed.
jemurray@shell:~$
jemurray@shell:~$
```

If a single `~` was entered, you will disconnect from the first host.  In this example, we connected to SFD and pressed `~.` which disconnected all `ssh` sessions:

```
jemurray@sdf:~ $
jemurray@sdf:~ $ Connection to shell.jasonmurray.org closed.
jemurray@jasons-mbp:~ $
```

This works for `ssh` sessions that are `N` deep by entering `~ * N-times`.  For example, if you were connected to a `ssh` server, chained through four servers, sending the `break sequence` would look like this:

```
~~~~B
````