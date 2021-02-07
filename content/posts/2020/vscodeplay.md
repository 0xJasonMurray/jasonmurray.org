---
title: "vsCode keyboard shortcut to run Python in the terminal"
date: 2020-07-28T13:39:52-05:00
toc: false
images:
categories:
  - tech
tags: 
  - python
  - vscode
---

I don't like moving my fingers off the keyboard.  No mouse, no arrow keys, no track pad.  Productive use of time dictates finger should not move far from the home-row keys.  This is why I [enabled](https://jasonmurray.org/posts/2020/vscodevim/) the [vim extension](https://marketplace.visualstudio.com/items?itemName=vscodevim.vim).  

To run a Python script within a terminal in vsCode you have to click the play button in the upper right corner of the screen:

![vscode](/images/vscodeplaybutton.png)

Remember rule #1, I don't want to move my fingers off the keyboard and the play button is too far away...

There is no default keyboard shortcut to replace pressing the green play button.   To create one, click the gear in the lower left corner of the screen and select `Keyboard Shortcuts`:

![vscode](/images/vscodeshortcutmenu.png)

Search for `run python in terminal` and click the `Python: Run Python File in Terminal`.  Create a keyboard shortcut sequence that works for you.  In my example, I use `CTRL+r` - again I want something that does not require me to move my fingers too far:

![vscode](/images/vscoderunshortcut.png)

Switch to a Python tab and press `ctrl+r` to run Python in the terminal window.

Note: I don't know why, but many other Google searches for this same topic yield a lot of results that are far too complicated.  

Since I switch back and forth between editor and terminal, I also created a shortcut to toggle between the two by mapping `ctrl+;`:

![vscode](/images/vscodetoggleterm.png)
