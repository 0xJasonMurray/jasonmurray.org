---
title: "Commands I use on a regular basis: find and exec"
date: 2020-08-08T11:13:48-05:00
toc: false
images:
categories:
  - tech
tags: 
  - find
  - commandsiuseonaregularbasis
---

In this example, we are using `find` to search for all files 24 hours old, outputting the contents, filtering the results through a Python program, and redirecting the final output to a text file. 

```
find . -type f -mtime -1 -exec cat {} \; | ~/findAllDynamicMacAddresses.py >  ~/dynamic-mac-address.txt
```

The `find` command is a multipurpose tool for locating files and directories.

`-type f` - Only look for files (not directories)

`-mtime 1` - Only return files that are 24 hours old or less.

`-exec cat {} \;` - Take the files returned from the find criteria and `cat` (display) the contents.  The string `{}` is replaced by the current file name being processed.  

`|` - Send the output from the left side of the pipe to the command on the right side.  In this example, the contents of the files returned from `find` and displayed by `cat` are processed through a Python script designed to locate all MAC addresses in a file.

`>` - Write all the output to a file

`~` - Variable that expands out to the currently logged in users home directory.  In this example: `/home/jemurray/`.

