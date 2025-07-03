---
title: "Return the base path or file name from a full path"
date: 2020-08-01T14:50:57-05:00
toc: false
images:
categories:
  - tech
tags: 
  - basename
  - dirname
---

Given the full path:

```
/usr/local/bin/foo
```

Return the file name:

```
jemurray@mbp-2019:~ $ basename /usr/local/bin/foo
foo
```

Return the directory path:

```
jemurray@mbp-2019:~ $ dirname /usr/local/bin/foo
/usr/local/bin
```


