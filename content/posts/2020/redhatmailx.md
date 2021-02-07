---
title: "Sending email on RedHat 7 with mailx"
date: 2020-10-09T13:41:34-05:00
toc: false
images:
categories:
  - tech
tags: 
  - redhat
  - mailx
---

To send email from a RedHat 7 server with no email server configured:

Install `mailx`:

```
sudo yum install mailx
```

When there is no email server configured, the `mailx` command will need to utilize a relay server with the `-S` option:

```
mailx -s "Test Email" -S smtp=smtp://mail.example.com jemurray@example.com
```


Full example:

```
$ echo "This is my test message" | mailx -v -s "Test Email" -S smtp=smtp://mail.example.com jemurray@example.com
Resolving host mail.example.com . . . done.
Connecting to 192.168.112.54:smtp . . . connected.
220 smtp5.mx.example.com ESMTP
>>> HELO RedHatHost.example.com
250 smtp5.mx.example.com
>>> MAIL FROM:<jemurray@RedHatHost.example.com>
250 sender <jemurray@RedHatHost.example.com> ok
>>> RCPT TO:<jemurray@example.com>
250 recipient <jemurray@example.com> ok
>>> DATA
354 go ahead
>>> .
250 ok:  Message 21861950 accepted
>>> QUIT
221 smtp5.mx.example.com
```
