---
title: "Enable Full Text RSS Feeds in Hugo"
date: 2021-11-01T19:21:46-05:00
toc: false
images:
categories:
  - tech
tags: 
  - hugo
  - rss
description: How to enable full text RSS feeds in Hugo.
---

By default, [Hugo](https://gohugo.io/) summarizes each article when generating the RSS feed. Not ideal if your the type of person who prefers to read the full content directly in an RSS reader.  This post will show you how to enable full text RSS feeds in Hugo.

Here's an example of a long article summarized in [Inoreader](https://www.inoreader.com/):

[![Image of Summarized article from RSS feed in Inoreader](/images/2021-11-01-19-39-24.png)](/images/2021-11-01-19-39-24.png)


Here's the same article after enabling full content RSS feeds:

[![Image of ](/images/2021-11-01-20-21-17.png)](/images/2021-11-01-20-21-17.png)


## Configuration Details

Create the following directory structure in the root of your Hugo site:

```text
mkdir layouts/_default
```

Output Example:

```
jemurray@phalanges:~/Documents/www-personal/current/jasonmurray.org $ mkdir layouts/_default
```

Then copy the following file into the `layouts/_default` directory.  It's is an updated version of the default [RSS template](https://raw.githubusercontent.com/gohugoio/hugo/master/tpl/tplimpl/embedded/templates/_default/rss.xml) with the appropriate modifications to generate full text RSS feeds:

```text
wget https://raw.githubusercontent.com/0xJasonMurray/jasonmurray.org/main/layouts/_default/rss.xml -O layouts/_default/rss.xml
```

Output Example:

```
jemurray@phalanges:~/Documents/www-personal/current/jasonmurray.org $ wget https://raw.githubusercontent.com/0xJasonMurray/jasonmurray.org/main/layouts/_default/rss.xml -O layouts/_default/rss.xml
--2021-11-01 19:23:12--  https://raw.githubusercontent.com/0xJasonMurray/jasonmurray.org/main/layouts/_default/rss.xml
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.110.133, 185.199.108.133, 185.199.109.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.110.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 1959 (1.9K) [text/plain]
Saving to: ‘layouts/_default/rss.xml’

layouts/_default/rss.xml                                    100%[=========================================================================================================================================>]   1.91K  --.-KB/s    in 0s

2021-11-01 19:23:12 (8.94 MB/s) - ‘layouts/_default/rss.xml’ saved [1959/1959]
```

For those curious, here's the `diff` between the original and the modified version of the `rss.xml` file:

```
jemurray@phalanges:~ $ diff rss.xml Documents/www-personal/current/jasonmurray.org/layouts/_default/rss.xml
35c35
<       <description>{{ .Summary | html }}</description>
---
>       <description>{{ .Content | html }}</description>
```