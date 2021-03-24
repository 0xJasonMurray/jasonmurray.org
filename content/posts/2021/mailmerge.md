---
title: "Using the Python mailmerge command to send customized emails"
date: 2021-03-24T14:35:38-05:00
toc: false
images:
categories:
  - tech
tags: 
  - python
  - mailmerge
description: Mailmerge is a Python command line tool to send individual customized email messages based on Jinja2 templates and an email database.
---

## Overview

[Mailmerge](https://pypi.org/project/mailmerge/) is a Python command line tool to send individual customized email messages based on Jinja2 templates and an email database.

## Details

Install `mailmerge`:

```
pip install mailmerge
```

Configure the system by creating sample configuration files:

```
(.venv) jemurray@phalanges:~/Documents/src/personalPython/mailmerge $ mailmerge --sample
Created sample template email "mailmerge_template.txt"
Created sample database "mailmerge_database.csv"
Created sample config file "mailmerge_server.conf"

Edit these files, then run mailmerge again.
```

The `mailmerge_template.txt` is the source of the email message.  The file is a Jinja2 template where variables are stored in `{{variables}}`. Each of these fields are replaced with data from the `mailmerge_database.csv`.

```
(.venv) jemurray@phalanges:~/Documents/src/personalPython/mailmerge $ cat mailmerge_template.txt
TO: {{email}}
SUBJECT: Testing mailmerge
FROM: My Self <myself@mydomain.com>

Hi, {{name}},

Your number is {{number}}.
```

The `mailmerge_database.csv` contains the values for the Jinja2 variables used in the `mailmerge_template.txt`:

```
(.venv) jemurray@phalanges:~/Documents/src/personalPython/mailmerge $ cat mailmerge_database.csv
email,name,number
jemurray@zweck.net,"Myself",17
test@example.com,"Test",42
```

Configure a valid email server in the `mailmerge_server.conf` file.  In this example, I use a Gmail account to send the emails:

```
(.venv) jemurray@phalanges:~/Documents/src/personalPython/mailmerge $ cat mailmerge_server.conf
# GMail
[smtp_server]
host = smtp.gmail.com
port = 465
security = SSL/TLS
username = example@gmail.com
```

My Gmail account uses 2-factor authentication. `mailmerge` does not support 2FA, therefore requires a static application password. Generate one from the Google account settings:

[![Image of ](/images/2021-03-24-14-42-40.png)](/images/2021-03-24-14-42-40.png)

Save the password:

[![Image of ](/images/2021-03-24-14-56-35.png)](/images/2021-03-24-14-56-35.png)


Once the templates, variable expansions, and servers are configured, validate the configuration and view an expanded email without sending anything (dry run mode):

```
(.venv) jemurray@phalanges:~/Documents/src/personalPython/mailmerge $ mailmerge
>>> message 1
TO: jemurray@zweck.net
SUBJECT: Testing mailmerge
FROM: My Self <myself@mydomain.com>
MIME-Version: 1.0
Content-Type: text/plain; charset="us-ascii"
Content-Transfer-Encoding: 7bit
Date: Wed, 24 Mar 2021 19:40:43 -0000

Hi, Myself,

Your number is 17.
```

Attempt to send 1 message as a test by adding the `--no-dry-run` option.  Make sure it looks good before sending to the entire list:

```
(.venv) jemurray@phalanges:~/Documents/src/personalPython/mailmerge $ mailmerge --no-dry-run
>>> password for example@gmail.com on smtp.gmail.com:
>>> message 1
TO: jemurray@zweck.net
SUBJECT: Testing mailmerge
FROM: My Self <myself@mydomain.com>
MIME-Version: 1.0
Content-Type: text/plain; charset="us-ascii"
Content-Transfer-Encoding: 7bit
Date: Wed, 24 Mar 2021 19:43:29 -0000

Hi, Myself,

Your number is 17.



>>> message 1 sent
>>> Limit was 1 message.  To remove the limit, use the --no-limit option.
```

After validating a single message looks ok, send to the entire list by adding the `--no-limit` option:

```
(.venv) jemurray@phalanges:~/Documents/src/personalPython/mailmerge $ mailmerge --no-dry-run --no-limit
>>> password for example@gmail.com on smtp.gmail.com:
>>> message 1
TO: jemurray@zweck.net
SUBJECT: Testing mailmerge
FROM: My Self <myself@mydomain.com>
MIME-Version: 1.0
Content-Type: text/plain; charset="us-ascii"
Content-Transfer-Encoding: 7bit
Date: Wed, 24 Mar 2021 19:44:52 -0000

Hi, Myself,

Your number is 17.



>>> message 1 sent
>>> message 2
TO: test@example.com
SUBJECT: Testing mailmerge
FROM: My Self <myself@mydomain.com>
MIME-Version: 1.0
Content-Type: text/plain; charset="us-ascii"
Content-Transfer-Encoding: 7bit
Date: Wed, 24 Mar 2021 19:44:55 -0000

Hi, Test,

Your number is 42.



>>> message 2 sent
```