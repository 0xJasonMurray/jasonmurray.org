---
title: "Domain redirection with AWS S3 buckets and Route 53 DNS"
date: 2021-04-21T06:44:30-05:00
toc: false
images:
categories:
  - tech
tags: 
  - aws
  - s3
description: How to redirect a domain using AWS S3 buckets and Route 53 DNS services.
---

## Overview

This guide walks though the steps required to redirect web requests for a domain such as `http://example.com/` to `http://example.org` using AWS S3 buckets and Route 53 DNS services.

## Details

Create a new S3 bucket:

[![Image of ](/images/2021-04-21-08-44-55.png)](/images/2021-04-21-08-44-55.png)


Create the bucket with the exact name of the website to redirect. In this example the domain name is `bsa957.org`:

[![Image of ](/images/2021-04-21-08-46-23.png)](/images/2021-04-21-08-46-23.png)


Enable public access by unchecking the `Block All Public Access` option:

[![Image of ](/images/2021-04-21-08-46-41.png)](/images/2021-04-21-08-46-41.png)

Keep the default settings for the rest of the options: 

[![Image of ](/images/2021-04-21-08-47-56.png)](/images/2021-04-21-08-47-56.png)

Create the bucket:

[![Image of ](/images/2021-04-21-08-49-37.png)](/images/2021-04-21-08-49-37.png)

Configure the bucket settings by clicking the domain `bsa957.org`:

[![Image of ](/images/2021-04-21-08-50-53.png)](/images/2021-04-21-08-50-53.png)

Select `properties`:

[![Image of ](/images/2021-04-21-08-52-40.png)](/images/2021-04-21-08-52-40.png)

Edit `static website hosting`:

[![Image of ](/images/2021-04-21-08-53-06.png)](/images/2021-04-21-08-53-06.png)

Enter the redirect settings:

- Static website hosting: enable
- Hosting type: Redirect requests for an object
- hostname: the FQDN of the host (without the http://)
- Protocol: https

[![Image of ](/images/2021-04-21-09-23-15.png)](/images/2021-04-21-09-23-15.png)

Edit the `Route 53` hosted domain:

NOTE: In this example, I purchased the domain through AWS. Domains purchased from external registrars must be delegated to AWS name servers first.

[![Image of ](/images/2021-04-21-08-55-46.png)](/images/2021-04-21-08-55-46.png)

Create a new DNS record:

[![Image of ](/images/2021-04-21-08-56-30.png)](/images/2021-04-21-08-56-30.png)

Create an `alias` record to the S3 bucket:

- Record name: leave this blank
- Record type: A
- Alias: move switch to on
- Route traffic to: Alias to S3 endpoint
- Location: US East (or whatever location was selected in the previous step)
- Website: The S3 bucket containing the website

[![Image of ](/images/2021-04-21-08-58-06.png)](/images/2021-04-21-08-58-06.png)


When the `https://bsa957.org` webpage is loaded, an HTTP `301 Moved Permanently` message is sent with an updated `Location: https://tmweb.troopmaster.com/mysite/troop957/` header. This tells the web browser to stop trying to load `bsa957.org` and fetch the content from `https://tmweb.troopmaster.com/mysite/troop957/`. 

We see what is happening behind the scenes with the `wget` command:

```
jemurray@phalanges:~ $ wget --server-response --spider bsa957.org
Spider mode enabled. Check if remote file exists.
--2021-04-21 15:47:52--  http://bsa957.org/
Resolving bsa957.org (bsa957.org)... 52.219.97.180
Connecting to bsa957.org (bsa957.org)|52.219.97.180|:80... connected.
HTTP request sent, awaiting response...
  HTTP/1.1 301 Moved Permanently
  x-amz-id-2: cwj2RIlWYMmPt1Fl5i8OUOymVoChvEzW71fYFoDhXT/zLUNQcfp+MLN9FdnvUnA/0PevLNKLzsE=
  x-amz-request-id: 9N4A6QSNZNDQGMJQ
  Date: Wed, 21 Apr 2021 20:47:54 GMT
  Location: https://tmweb.troopmaster.com/mysite/troop957/
  Content-Length: 0
  Server: AmazonS3
```