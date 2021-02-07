---
title: "Removing unsafe characters with Python and regex"
date: 2020-07-18T18:03:25-05:00
toc: false
images:
categories:
  - tech
tags: 
  - python
  - regex
---

While working on a program which uses data from an internal CMDB to build `ssh` [aliases](/posts/2020/bashfunctions/), I ran into a problem.  Some of the devices have special characters in the hostname, for example:

```
DEBUG pre-fix: wcm-449-wu-fw-0/stby
DEBUG pre-fix: wcm-449-wu-fw-0/act
```

To remove the unsafe characters, the `hostname` variable is passed to a `cleaning` function each time it is set:

{{< highlight python  "linenos=table" >}}
def fix_hostname(hostname):
    hostname = re.sub(r'[\\/:"*?<>|]+', "-", hostname)
    hostname = hostname.lower()
    return hostname

hostname = fix_hostname(device["name"])
{{< / highlight >}}

The unsafe characters are converted to `-`:

```
DEBUG post-fix: wcm-449-wu-fw-0-stby
DEBUG post-fix: wcm-449-wu-fw-0-act
```
