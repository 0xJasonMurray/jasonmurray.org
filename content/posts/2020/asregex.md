---
title: "Basic aspath-regex"
date: 2020-09-02T17:08:55-05:00
toc: false
images:
categories:
  - tech
tags: 
  - asn
  - regex
---

## Overview

The world of regular expressions seems like magic or voodoo to many newcomers.  RegEx at its most basic form is nothing more then simple pattern matching expressions.  I see many seasoned network engineers overlook this powerful tool because of the perceived complexity.  Sure, there are some very complex regular expressions and the language is limitless.   Don't be overwhelmed, start with the basics and work up to more complex patterns as needed.  

To learn regEx, one first needs a real world task to practice on.   In the following examples, we will do basic ASN pattern matching on a Juniper router.   

## RegEx Basics

- `^` = Match the beginning of a line.
- `$` = Match the end of a line.
- `.` = Match a single character.
- `.*` = Match 0 or more characters.
- `.+` = Match 1 or more characters.


## Examples


Search for all routes with the AS path that matches exactly: `19782 11537 30103`


```
juniper-router> show route aspath-regex "^19782 11537 30103$"

8.5.128.0/23       *[BGP/170] 1w0d 04:30:45, MED 0, localpref 200
                      AS path: 19782 11537 30103 I, validation-state: unknown
                    > to 149.165.254.141 via xe-1/2/0.944
```

Search for all routes that originated from Hurricane Electric (ASN 6939):

```
juniper-router> show route aspath-regex .*6939$

5.152.182.0/24     *[BGP/170] 16:59:14, MED 0, localpref 100
                      AS path: 3356 6939 I, validation-state: unknown
                    > to 4.28.92.177 via xe-1/2/3.0
```

Search for routes that terminate at Hurricane Electric (ASN 6939) which traverse through Telia (ASN 1299):

```
juniper-router> show route aspath-regex ".+ 1299 .+ 6939$"

5.152.182.0/24      [BGP/170] 1w5d 08:15:12, MED 13020, localpref 100
                      AS path: 174 1299 6939 6939 I, validation-state: unknown
```

Search for all routes through the directly connected service provider Cogent (ASN 174):

```
juniper-router> show route aspath-regex ^174.*

1.0.0.0/24         *[BGP/170] 3d 12:17:44, MED 2020, localpref 100
                      AS path: 174 13335 I, validation-state: unknown
                    > to 38.104.162.77 via xe-0/0/2.0
```

Show all routes that only originate and are directly connected to the Indiana GigaPOP (ASN 19782):

```
juniper-router> show route aspath-regex ^19782$

149.165.128.0/17   *[BGP/170] 1w0d 07:28:45, MED 0, localpref 200
                      AS path: 19782 I, validation-state: unknown
                    > to 149.165.254.141 via xe-1/2/0.944
```

We sometimes forget the elegance in simplicity.   Start out easy, then add complexity as necessary.
