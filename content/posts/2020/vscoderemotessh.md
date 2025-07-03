---
title: "Edit files on remote hosts with vsCode and SSH"
date: 2020-08-04T15:52:08-05:00
toc: false
images:
categories:
  - tech
tags: 
  - vscode
  - ssh
  - remotessh
---

The [Remote - SSH](https://code.visualstudio.com/docs/remote/ssh) extension in Visual Studio Code allows us to edit files on a remote host over an encrypted SSH connection.

To get started, install the Remote SSH extension:

![vscode remote ssh](/images/vscinstallsshextension.png)

Click the green `><` block in the lower left corner and add a host to SSH into:

![vscode remote ssh](/images/vscconnecttoremotehost.png)

vsCode will open the remote connection, copy the necessary software, and setup the session:

![vscode remote ssh](/images/vscopeningremotessh.png)

Browse the remote file system, edit files, and interact with a remote terminal like vsCode is running on the local workstation:

![vscode remote ssh](/images/vscrunninghelloremote.png)