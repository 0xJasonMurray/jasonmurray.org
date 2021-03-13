---
title: "Newlines in markdown"
date: 2021-03-12T18:48:42-06:00
toc: false
images:
categories:
  - tech
tags: 
  - markdown
description: Markdown ignores newlines, use a \ to disable this behavior.
---

Markdown ignores newlines and concatenates short lines of text together.  For instance, take the following text in a Markdown file:

```
The Markdown engine takes multiple lines,
which are not separated by a double space,
and concatinates them into a single line.
Placing a \ after each line, disables this
behavior so that each line is separated.
```

These 5 separate lines, when processed through the Markdown engine, will output a single line of text:

> The Markdown engine takes multiple lines,
which are not separated by a double space,
and concatinates them into a single line.
Placing a \ after each line, disables this
behavior so that each line is separated.

Maintain the original formatting by placing a `\` after each line:

```
The Markdown engine takes multiple lines,\
which are not separated by a double space,\
and concatinates them into a single line.\
Placing a \ after each line, disables this\
behavior so that each line is separated.
```

The `\` maintains the original format without having to use code blocks or preformatting tags:

> The Markdown engine takes multiple lines,\
> which are not separated by a double space,\
> and concatinates them into a single line.\
> Placing a \ after each line, disables this\
> behavior so that each line is separated.