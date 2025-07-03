---
title: "Turn c into assembly"
date: 2021-03-09T14:09:27-06:00
toc: false
images:
categories:
  - tech
tags: 
  - gcc
  - assembly
description:
---

## Overview

Turn `c` code into assembly language with `gcc`.

## Details

Create a `hello.c` file with the following contents or any other `c` program to convert into `assembly` language:

```text
#include <stdio.h>

void main () {
	printf("Hello world!\n");
}
```

Generate the `assembly` output:

```
jemurray@shell:~$ gcc -S hello.c
jemurray@shell:~$ ls -al hell*
-rw-r--r-- 1 jemurray jemurray  64 Mar  9 19:59 hello.c
-rw-r--r-- 1 jemurray jemurray 438 Mar  9 19:59 hello.s
```

Examine the `assembly` code:

```
jemurray@shell:~$ cat hello.s
	.file	"hello.c"
	.text
	.section	.rodata
.LC0:
	.string	"Hello world!"
	.text
	.globl	main
	.type	main, @function
main:
.LFB0:
	.cfi_startproc
	pushq	%rbp
	.cfi_def_cfa_offset 16
	.cfi_offset 6, -16
	movq	%rsp, %rbp
	.cfi_def_cfa_register 6
	leaq	.LC0(%rip), %rdi
	call	puts@PLT
	nop
	popq	%rbp
	.cfi_def_cfa 7, 8
	ret
	.cfi_endproc
.LFE0:
	.size	main, .-main
	.ident	"GCC: (Debian 8.3.0-6) 8.3.0"
	.section	.note.GNU-stack,"",@progbits
```

Why would non-low level programmers care? Because it's interesting to understand the dark magic wizards use to speak with the processors or at least as close as us mere mortals can understand. But seriously, most people would not write `assembly` code like `gcc` generates. However, it is still fun to see the individual instruction `opcodes` the processor executes to print a single line of text. 