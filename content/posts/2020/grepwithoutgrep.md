---
title: "grep for a process without grep returning grep"
date: 2020-07-25T14:37:14-05:00
toc: false
images:
categories:
  - tech
tags: 
  - grep
---

Searching for a process with `grep`, in addition to the search term, the output also includes the `grep` statement itself:

```
jemurray@shell:~$ ps -ef | grep bgp
jemurray  8054 23273  0 19:38 pts/7    00:00:00 grep bgp
jemurray 25310 14989  0 Jul22 pts/2    00:28:29 ./bgpalerter-linux-x64
```

By putting one of the search term characters between `[]`'s, `grep` is no longer returned in the results:

```
jemurray@shell:~$ ps -ef | grep [b]gp
jemurray 25310 14989  0 Jul22 pts/2    00:28:29 ./bgpalerter-linux-x64
```

