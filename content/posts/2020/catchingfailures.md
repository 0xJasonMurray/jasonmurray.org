---
title: "Catching script execution failures with a semicolon"
date: 2020-10-20T06:27:35-05:00
toc: false
images:
categories:
  - tech
tags: 
  - bash
  - scripting
---

In `bash` shell scripting, the `;` (seimicolon) is know as the command separator.  It separates two commands that run sequentially, one after the other. For example: `command-one; command-two`.   

By using the `;`, we can create a `watch-dog` script to send out an alert if the previous command stops executing.  In this example, I want to be notified if the `./longRunningScript.py` exits for any reason:

```
./longRunningScript.py; echo "The script ended" | mail -s "Ut oh, the script ended unexpectedly!" -S smtp=smtp://mail.example.com -v jemurray@example.com
```

A few hours later I received an email from the `watch-dog`.  After reviewing the console output, it looks like a login failure caused the script to crash:

```
Traceback (most recent call last):
  File "./longRunningScript.py", line 17, in <module>
    net_connect = Netmiko(host="192.168.204.254", username="jemurray", password=password, device_type="cisco_ios")
  File "/usr/local/lib/python3.6/site-packages/netmiko/ssh_dispatcher.py", line 312, in ConnectHandler
    return ConnectionClass(*args, **kwargs)
  File "/usr/local/lib/python3.6/site-packages/netmiko/cisco/cisco_ios.py", line 17, in __init__
    return super().__init__(*args, **kwargs)
  File "/usr/local/lib/python3.6/site-packages/netmiko/base_connection.py", line 346, in __init__
    self._open()
  File "/usr/local/lib/python3.6/site-packages/netmiko/base_connection.py", line 351, in _open
    self.establish_connection()
  File "/usr/local/lib/python3.6/site-packages/netmiko/base_connection.py", line 920, in establish_connection
    self.remote_conn_pre.connect(**ssh_connect_params)
  File "/usr/local/lib/python3.6/site-packages/paramiko/client.py", line 446, in connect
    passphrase,
  File "/usr/local/lib/python3.6/site-packages/paramiko/client.py", line 764, in _auth
    raise saved_exception
  File "/usr/local/lib/python3.6/site-packages/paramiko/client.py", line 751, in _auth
    self._transport.auth_password(username, password)
  File "/usr/local/lib/python3.6/site-packages/paramiko/transport.py", line 1509, in auth_password
    return self.auth_handler.wait_for_response(my_event)
  File "/usr/local/lib/python3.6/site-packages/paramiko/auth_handler.py", line 250, in wait_for_response
    raise e
paramiko.ssh_exception.AuthenticationException: Authentication failed.
Resolving host mail.example.com . . . done.
Connecting to 192.168.112.54:smtp . . . connected.
220 smtp5.mx.example.com ESMTP
>>> HELO host.example.com
250 smtp5.mx.example.com
>>> MAIL FROM:<jemurray@host.example.com>
250 sender <jemurray@host.example.com> ok
>>> RCPT TO:<jemurray@example.com>
250 recipient <jemurray@example.com> ok
>>> DATA
354 go ahead
>>> .
250 ok:  Message 22076930 accepted
>>> QUIT
221 smtp5.mx.example.com
```