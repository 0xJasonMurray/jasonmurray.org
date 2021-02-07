---
title: "Installing python3 on macOS using Homebrew (don't use the system version)"
date: 2020-05-17T07:46:39-05:00
toc: false
images:
categories:
  - tech
tags: 
  - macOS
  - python3
  - homebrew
---

I recommend avoiding the version of python that comes preinstalled on macOS for the following reasons:

- It is not the latest version
- Older releases of macOS do not have python3
- Adding packages / libraries may break system functionality
- Separation of development vs. system 

One of the easiest ways to install and manage the latest version of python3, and other software, is to use the [Homebrew](https://brew.sh) package manager.  

Install Homebrew:

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
```

Install python3:

```
brew install python3
```

Validate python3 is working:

```
jemurray@jasons-mbp ~ % /usr/local/bin/python3
Python 3.7.7 (default, Mar 10 2020, 15:43:33)
[Clang 11.0.0 (clang-1100.0.33.17)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> print("Hello world!")
Hello world!
>>>
```
