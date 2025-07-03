---
title: "Syntax highlighting in Hugo"
date: 2020-07-20T11:14:31-05:00
toc: false
images:
categories:
  - tech
tags: 
  - hugo
  - shortcodes
  - syntaxhighlight
---

The back-ticks ` or ``` are used to keep original text formatting within Hugo rendered pages.  

Example 1, single back-tick:

`A single back-tick at the start and end of the line`


Example 2, triple back-tick:

```
Three back-ticks above and three back-ticks below this line
```

Using back-ticks for code keeps the formatting, but the output is not pretty or easy to read:

```
def fix_hostname(hostname):
    hostname = re.sub(r'[\\/:"*?<>|]+', "-", hostname)
    hostname = hostname.lower()
    return hostname

hostname = fix_hostname(device["name"])
```

In Hugo, the [highlight short-code](https://gohugo.io/content-management/syntax-highlighting/#highlight-shortcode) is used for syntax highlighting.  Wrap the code with the following lines:

```
{{</* highlight python  "linenos=table" */>}}

{{</* / highlight */>}}
```

It will look like this in the markdown file:

```
{{</* highlight python  "linenos=table" */>}}
def fix_hostname(hostname):
    hostname = re.sub(r'[\\/:"*?<>|]+', "-", hostname)
    hostname = hostname.lower()
    return hostname

hostname = fix_hostname(device["name"])
{{</* / highlight */>}}
```

Which results in this:

{{< highlight python  "linenos=table" >}}
def fix_hostname(hostname):
    hostname = re.sub(r'[\\/:"*?<>|]+', "-", hostname)
    hostname = hostname.lower()
    return hostname

hostname = fix_hostname(device["name"])
{{< / highlight >}}
`


On a side note, to display unprocessed short-codes,  put a `/*  */` between the curly brackets:

```
{{</* /* highlight python  "linenos=table" */ */>}}
```