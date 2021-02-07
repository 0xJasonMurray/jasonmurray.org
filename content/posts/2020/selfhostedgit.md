---
title: "Self hosting a git repository"
date: 2020-06-13T17:37:11-05:00
toc: false
images:
categories:
  - tech
tags: 
  - git
---

# Summary

`git`, a free and open source distributed version control system, has a number online services for hosting projects.  When control over data or additional features are required, self-hosting a git repository may be a better choice.   With a simple Internet connected Linux server running SSH, setting up a remote `git` repository is easy to do.


On the remote server:

```
mkdir git
cd git
mkdir jasonmurray.org.git
cd jasonmurray.org.git/
git init --bare
```

On the local source:

```
cd ~/jasonmurray.org
git init
git add .
git commit -m 'initial commit'
git remote add origin jemurray@shell.jasonmurray.org:/home/jemurray/git/jasonmurray.org.git
git push origin master
git status
```

# Details

## On the remote server

Keep the configuration simple by putting the `git` repository in the users $HOME directory:

```
jemurray@shell:~$ pwd
/home/jemurray
```

Create a directory to store all `git` repositories:

```
jemurray@shell:~$ mkdir git
jemurray@shell:~$ cd git
```

Create the `git` project:

```
jemurray@shell:~/git$ mkdir jasonmurray.org.git
jemurray@shell:~/git$ cd jasonmurray.org.git/
```

Initialize a bare `git` repository:

```
jemurray@shell:~/git/jasonmurray.org.git$ git init --bare
Initialized empty Git repository in /home/jemurray/git/jasonmurray.org.git/
```

Confirm the repo is created:

```
jemurray@shell:~/git/jasonmurray.org.git$ ls -al
total 40
drwxr-xr-x 7 jemurray jemurray 4096 Jun 12 01:33 .
drwxr-xr-x 3 jemurray jemurray 4096 Jun 12 01:33 ..
drwxr-xr-x 2 jemurray jemurray 4096 Jun 12 01:33 branches
-rw-r--r-- 1 jemurray jemurray   66 Jun 12 01:33 config
-rw-r--r-- 1 jemurray jemurray   73 Jun 12 01:33 description
-rw-r--r-- 1 jemurray jemurray   23 Jun 12 01:33 HEAD
drwxr-xr-x 2 jemurray jemurray 4096 Jun 12 01:33 hooks
drwxr-xr-x 2 jemurray jemurray 4096 Jun 12 01:33 info
drwxr-xr-x 4 jemurray jemurray 4096 Jun 12 01:33 objects
drwxr-xr-x 4 jemurray jemurray 4096 Jun 12 01:33 refs
```

Copy the full path of the repository for the next steps:

```
jemurray@shell:~/git/jasonmurray.org.git$ pwd
/home/jemurray/git/jasonmurray.org.git
```

## On the local workstation

Initialize the master source `git` repository:

```
jemurray@mbp-2019:~/Documents/www-personal/current/jasonmurray.org $ git init
Initialized empty Git repository in /Users/jemurray/Documents/www-personal/current/jasonmurray.org/.git/
```

Add files to the new `git` repository: 

```
jemurray@mbp-2019:~/Documents/www-personal/current/jasonmurray.org $ git add .
```

Create the initial commit:

```
jemurray@mbp-2019:~/Documents/www-personal/current/jasonmurray.org $ git commit -m 'initial commit'
[master (root-commit) 391fc5b] initial commit
 Committer: Jason Murray <jemurray@zweck.net>

 826 files changed, 64344 insertions(+)
 create mode 100644 .DS_Store
 create mode 100644 archetypes/default.md
 create mode 100644 archetypes/ondeck.md
 create mode 100644 archetypes/posts.md
 create mode 100644 config.toml
 create mode 100644 config.toml-hermit
 create mode 100644 config.toml-hugoCoder
 create mode 100644 config.toml-minimal
 create mode 100644 content/.DS_Store
 ...
 ```

 Add the remote repository:

 ```
 jemurray@mbp-2019:~/Documents/www-personal/current/jasonmurray.org $ git remote add origin jemurray@shell.jasonmurray.org:/home/jemurray/git/jasonmurray.org.git
 ```

 Push files to the remote repository:

 ```
 jemurray@mbp-2019:~/Documents/www-personal/current/jasonmurray.org $ git push origin master
Enumerating objects: 1049, done.
Counting objects: 100% (1049/1049), done.
Delta compression using up to 12 threads
Compressing objects: 100% (904/904), done.
Writing objects: 100% (1049/1049), 102.76 MiB | 1.21 MiB/s, done.
Total 1049 (delta 391), reused 0 (delta 0)
remote: Resolving deltas: 100% (391/391), done.
To shell.jasonmurray.org:/home/jemurray/git/jasonmurray.org.git
 * [new branch]      master -> master
 ```

 Check the status:

 ```
 jemurray@mbp-2019:~/Documents/www-personal/current/jasonmurray.org $ git status
On branch master
nothing to commit, working tree clean
```
