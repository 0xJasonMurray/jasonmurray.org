---
title: "Inserting date and time in vim using insert-mode mapping"
date: 2020-07-03T14:01:17-05:00
toc: false
images:
categories:
  - tech
tags: 
  - vim
  - inoremap
---

Using a text file and `vim`, I keep a log file of daily events happening in my life and at work.  Before every new event, I insert the current date and time.  By using insert-mode key mappings, the date and time is automatically generated and inserted.

Add the following line to the ~/.vimrc file:

```
inoremap idt <C-R>=strftime("%c")<CR>
```

Open `vim` enter insert mode, and type: `idt`

```
Wed Jul  1 22:09:31 2020 - The date and time was inserted by pressing idt while in insert mode
~
~
~
~
~
~
~
~
~
~
~
~
~
~
-- INSERT --
```