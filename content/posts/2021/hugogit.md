---
title: "Using git as a historical time machine for jasonmurray.org"
date: 2021-02-07T11:54:28-06:00
toc: false
images:
categories:
  - tech
tags: 
  - hugo
  - git
  - rsync
description: By using markdown and git, I can track changes to my website over time.
---

# Overview

I enjoy watching how my online presence changes over time.  To facilitate keeping this historical record, the content on `jasonmurray.org` is written in standard markdown and tracked with the `git` version control system.   Until recently, I used a [self-hosted git repo](https://jasonmurray.org/posts/2020/selfhostedgit/) to keep unpublished drafts and private parts of the site from becoming public.  

To simplify the management infrastructure, I moved from a self-hosted repository to one hosted at [GitHub](https://github.com/0xJasonMurray/jasonmurray.org).  To keep unpublished information private, a `.gitignore` file excludes the appropriate directories from public consumption.


I streamlined `git commit` and `rsync` publishing into a single command with the use of `git hooks`.  When changes are committed into the git repository, a `git hook` builds the html content and `rsync` copies it to the remote server.   


# Details

The `git` `post-commit` hook `$HUGOROOT/.git/hooks/post-commit`, builds the static html pages and `rsync` copies the files to the remote web server:

```
jemurray@jasons-mbp:~/Documents/www-personal/current/jasonmurray.org $ cat .git/hooks/post-commit
#!/bin/sh

##
## After commit, push changes to the live web server
##

cd ~jemurray/Documents/www-personal/current/jasonmurray.org
hugo --cleanDestinationDir && rsync -av --exclude '.DS_Store' --delete ~jemurray/Documents/www-personal/current/jasonmurray.org/public/ shell.jasonmurray.org:/var/www/html
```

A complete `git` commit looks like this:

```
jemurray@jasons-mbp:~/Documents/www-personal/current/jasonmurray.org $ git status
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
	modified:   config.toml

no changes added to commit (use "git add" and/or "git commit -a")

jemurray@jasons-mbp:~/Documents/www-personal/current/jasonmurray.org $ git add .

jemurray@jasons-mbp:~/Documents/www-personal/current/jasonmurray.org $ git commit -m "Updated config"
Start building sites …

                   |  EN
-------------------+-------
  Pages            | 1298
  Paginator pages  |    0
  Non-page files   |    0
  Static files     |  512
  Processed images |    0
  Aliases          |    0
  Sitemaps         |    1
  Cleaned          |    0
```


The process is streamlined even further with Visual Studio Code's built-in `git` management tools:

![](/images/2021-02-07-10-11-38.png)