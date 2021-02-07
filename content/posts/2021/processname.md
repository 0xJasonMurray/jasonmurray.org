---
title: "Change process display name"
date: 2021-02-04T12:51:41-06:00
toc: false
images:
categories:
  - tech
tags: 
  - linux
  - c
  - process
description: By changing the argv[0] variable (language specific), we can change the visible name of a process.
---

# Overview

By changing the argv[0] variable (language specific), we can change the visible name of a process.

# Details


The program:

```
#include <unistd.h>
#include <string.h>
#include <stdio.h>

int main(int argc, char *argv[]) {

    printf("pid: %i\n", getpid());

    char psname[] = "systemd";
    strncpy(argv[0],psname,strlen(argv[0]));

    sleep(300);

    return 0;
}
```

Compile:

```
gcc ./myOriginalProgramName.c -o myOriginalProgramName
```

Execute with the name `./myOriginalProgramName`: 

```
jemurray@home-server:~/processname$ ./myOriginalProgramName
pid: 31514
```


Examine the current process table, the name has been changed from `myOriginalProgramName` to `systemd`:

```
jemurray@home-server:~$ ps -ef | grep 31514
jemurray 31514 29114  0 13:12 pts/8    00:00:00 systemd
```

