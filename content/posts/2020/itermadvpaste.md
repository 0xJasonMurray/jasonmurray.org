---
title: "Avoid accidental pasting with iTerm2"
date: 2020-11-18T15:47:44-06:00
toc: false
images:
categories:
  - tech
tags: 
  - iterm2
  - advancedpaste
  - paste
---

iTerm2 has a feature called "Advanced Paste...".  This feature allows the user to review and modify the copy-and-paste buffer before the paste takes place.  

For example, the command `./therightcommand.sh` is entered in one terminal window and needs to be pasted into another.  The text is selected and `control+v` is pressed to copy the text.

![](/images/2020-11-18-15-48-07.png)

Then `option+command+c` is pressed to enter the `Advanced Paste...` mode.   Whoops, `ctrl+c` was accidentally pressed above, which is the wrong copy command.  This mistake is easily caught with advanced pasted in iTerm:

![](/images/2020-11-18-15-48-50.png)


Editing is not limited to a single command or single word.  This is especially important for network engineers who regularly paste network configurations over the CLI:

![](/images/2020-11-18-16-02-12.png)