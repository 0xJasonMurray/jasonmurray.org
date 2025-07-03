---
title: "Remove files from git repository without removing local files"
date: 2021-02-10T06:42:32-06:00
toc: false
images:
categories:
  - tech
tags: 
  - git
---

## Overview

Running `git rm <file>` without the `--cached` option will delete files from a `git` repository **and** the local file system.  To remove files from a `git` repository **only**, use the `--cached` command line argument.

## Locate and remove a single file

Locate files to remove:

```text
git ls-files
```

Output:

```text
jemurray@jasons-mbp:~/Documents/www-personal/current/jasonmurray.org $ git ls-files
...
content/.DS_Store
...
```

Remove single file from the `git` repository without removing local file:

```text
git rm --dry-run --cached content/.DS_Store
```

Output:

```text
jemurray@jasons-mbp:~/Documents/www-personal/current/jasonmurray.org $ git rm --dry-run --cached content/.DS_Store
rm 'content/.DS_Store'
```

Confirm the local file still exists:

```text
jemurray@jasons-mbp:~/Documents/www-personal/current/jasonmurray.org $ ls -ald .DS_Store
-rw-r--r--@ 1 jemurray  staff  10244 Jan 31 15:57 .DS_Store
```

## Remove files from .gitignore file

Files and directories included in the `.gitignore` will be excluded from tracking in a `git` repository.   But what if `.gitignore` was created after the files were added?   The following command will look at the contents of `.gitignore` and automatically remove matching files from the `.git` repository, not the local file system.  Note: the `--dry-run` command line argument confirms the actions without removing any files, remove after validation:

```text
git rm --dry-run --cached `git ls-files -i -X .gitignore`
```

Output:

```text
jemurray@jasons-mbp:~/Documents/www-personal/current/jasonmurray.org $ git rm --dry-run --cached `git ls-files -i -X .gitignore`
rm 'content/.DS_Store'
rm 'content/posts/loap/loapgeneration.md'
rm 'content/posts/loap/loapintroduction.md'
rm 'static/.DS_Store'
```