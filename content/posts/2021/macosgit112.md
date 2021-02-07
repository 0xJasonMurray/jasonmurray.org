---
title: "After macOS upgrade, git is broken again"
date: 2021-02-05T16:21:44-06:00
toc: false
images:
categories:
  - tech
tags: 
  - git
  - macos
  - bigsur
  - xcode
---

# Overview

After the BigSur 11.2 upgrade `git` stopped working.  This is the [same problem I had upgrading from 10.14.x to 10.15.x in the past](/posts/2020/gitmacos1015/).

# Details

`git` stopped working after the BigSur 11.2 upgrade:

```
jemurray@jasons-mbp:~/Documents/www-personal/current/jasonmurray.org $ git status
xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun
```

Fix the problem by running the `xcode` command line tool install:

```
jemurray@jasons-mbp:~/Documents/www-personal/current/jasonmurray.org $ xcode-select --install
xcode-select: note: install requested for command line developer tools
```

The command above starts the GUI install:

![](/images/2021-02-05-16-22-25.png)

3+ hours over a 1Gbp/s link:

![](/images/2021-02-05-16-23-08.png)

It only took about 5 minutes to install:

![](/images/2021-02-05-16-28-50.png)

Git is working again:

```
jemurray@jasons-mbp:~/Documents/www-personal/current/jasonmurray.org $ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   content/now.md
```

