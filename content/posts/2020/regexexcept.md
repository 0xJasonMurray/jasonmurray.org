---
title: "Excluding matches with a regex exception"
date: 2020-12-14T13:34:18-06:00
toc: false
images:
categories:
  - tech
tags: 
  - regex
---

# Overview

I am embarrassed to admit this, but I have never used a regex exclusion `[^...]` before.  Regular expressions have been a part of my career for as far back as I can remember.   I actually thought I had a pretty solid understanding of all the options available.   But, exclusions... I somehow missed them.

With this construct we can find everything EXCEPT `<insert pattern match here>`, by using the exception `[^...]` character class.


# Examples

Search the string `a` for all characters, EXCEPT a, b, c, d, e, f (no match):

```
jemurray@mbp-2019:~ $ echo "a" | grep '[^abcdef]'
```

Repeat the same type of search again, this time for the character `z` which is not on the exclusion list `[^abcdef]`.  A match is found:

```
jemurray@mbp-2019:~ $ echo "z" | grep '[^abcdef]'
z
```


