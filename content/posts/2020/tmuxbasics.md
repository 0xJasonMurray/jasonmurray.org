---
title: "tmux basics"
date: 2020-11-13T15:48:06-06:00
toc: false
images:
categories:
  - tech
tags: 
  - tmux
---

# Overview

tmux is a terminal multiplexer. It lets you switch between several programs in one terminal session, detach them (while everything is still running in the background) and reattach them to a different terminal.  If you use the console command line, especially from remote, this is a must have tool.

Start tmux, run a long command, detach and come back days later, it will still be there (this alone is enough reason to use tmux):

![](/images/2020-11-13-15-51-55.png)


# Basic Commands:

95% of tmux commands I use are as follows:

- Start tmux: `tmux`
- Start tmux with a name: `tmux new -s myremotesession`
- Create a new windows: `ctrl-b c`
- Switch to the next window: `ctrl-b n`
- Switch to the previous window: `ctrl-b p`
- Close current window: `ctrl-d`
- Detach current session: `ctrl-b d`
- List existing tmux sessions: `tmux ls`
- Attach to an existing named session: `tmux attach -t myremotesession`
- Attach to an existing unnamed session: `tmux attach -t <number from tmux ls>`
- Scroll back: `ctrl-b PAGEUP`
- Enter copy-paste mode: `ctrl-b [`
- Select copy-paste text: `ctrl-w`
- Paste text in copy-paste buffer: `ctrl-b ]`


