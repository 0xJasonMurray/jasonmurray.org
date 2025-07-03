---
title: "Matching and printing regex groups in sed"
date: 2021-02-15T15:19:08-06:00
toc: false
images:
categories:
  - tech
tags: 
  - sed
description: Sed one-liner to capture and print regular expression group match.
---

## Overview

A CLI one-liner using regular expressions and `sed` to capture a group of information and print it.  I typically use `python` to build complex regular expression pattern matching and grouping.  In this example, a simple one-liner pulls data out of a text file.

## Details

The input file is a list of Markdown links:

```
jemurray@jasons-mbp:~ $ head -1 scratch.txt
[Schedule requirements gathering meeting for WSA](https://example.com)
```

The `sed` command pulls all the text between the square-brackets `[...]`.

- `-n` - Surpasses output; by default, `sed` prints all data after a match is made.
- `s` - Search
- `\[` - Match a left-bracket
- `\(` - Start the group match
- `.*` - Match anything up to the next more specific match character
- `\)` - End the group match (this group match will be printed to the console)
- `\](.*$` - Match the right-bracket, left-parentheses, and any text to the end of the line.
- `/\1/` - print the information stored in the first group match `()`
- `p` - print the match  

```
jemurray@jasons-mbp:~ $ cat scratch.txt | sed -n "s/\[\(.*\)\](.*$/\1/p" | head -1
Schedule requirements gathering meeting for WSA
```

I love the command line!
