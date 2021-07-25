---
title: "Rebuilding the Laptop in 2021"
date: 2021-01-01T11:17:50-06:00
toc: false
images:
categories:
  - tech
tags: 
  - laptop
  - rebuild
---
# Overview

My first new years resolution.  Rebuild my daily driver laptop.

# Rebuild Details

- Before starting the rebuild:
  - Validate iCloud drive sync is functioning properly.   Log into the web interface to spot check a  few files.
  - Validate TimeMachine has recently run a backup.
  - Unmount all external backup disks.
  - Dump list of /Applications: `ls -al /Applications`
  - Dump list of software installed with `brew`: `brew list`
  - Make sure the software list is backed up or synced with iCloud before rebooting.
- Shutdown and power off.
- Press the power button.
- Immediately press and hold: `<command>+r`.
- Connect to the Internet by selecting the wireless network in the upper right corner.
- Select Disk Utility
  - Note: There is no turning back from the following steps, all existing data will be deleted.
  - Delete all existing partitions (Macintosh HD and Macintosh HD - Data).
  - Create a new single partition.
  - Erase the new partition with an APFS file system.
  - Exit Disk Utility
- Select: Reinstall macOS (reinstall a new copy of macOS)
  - Unfortunately, this method will not install the latest OS.  You must install the same macOS version that came with the system originally first.
- Post Installation:
  - Create the local user account.
  - Don't enable iCloud at this point.  
  - Install all the latest patches.
- Start the upgrade to the latest macOS version.
- System Preferences
  - Enable firewall, it is off by default.
  - Swap `<control>` and `<cap-locks>`.
  - Setup keyboard short cuts to switch desktops with `<control> + ##`
  - Uncheck automatically rearrange space in Mission Control settings.
  - Enable TimeMachine and display in menu bar
  - Accounts: Enable Google Contacts
- Enable iCloud
  - Enable iCloud Drive and sync Documents folder.
  - Note: iCloud drive may take hours for files to start showing up.   Give it time.
  - Enable KeyChain - it will prompt for passwords.
- Change shell from `zsh` to `bash` (`chsh -s /bin/bash`)
- Setup dotfiles:
  - Symlink .ssh (ln -s ~/Documents/dotfiles/.ssh ./)
    - Fix permissions on private key: `chmod 600 id_rsa`
  - Symlink .bash_profile (ln -s ~Documents/dotfiles/.bash_profile ./)
- Software installation:
  - Brew
    - hugo
    - sipcalc
    - autossh
    - pandoc
    - basictex (`brew cask install basictex`)
    - gs
    - grepcidr
    - wget
    - tor
    - torsocks
    - mosh
    - gpg
    - jq
    - python-yq
    - iperf3
    - telnet
    - coreutils
    - mtr
    - pv
    - exiftool
    - pstree
    - htop
  - LastPass (macOS and plugins)
    - Allow LastPass to monitor input from keyboard
  - iTerm2
    - Enable terminal logging (plain text)
    - Set unlimited scroll-back
    - Mouse follows focus: prefs -> pointer -> general
    - Don't adjust window when changing font size: prefs -> general -> window -> font
  - Chrome
    - Sign in and enable sync
  - Teams
  - Slack (from AppStore)
  - Discord
  - Notion
  - OneNote
    - Open Notebook: Jason's Notes
  - Zoom
  - vscode
    - Login with GitHub account
    - Enable Configuration Sync
    - Extensions:
      - Markdown All in One
      - Python
      - Spell Right
      - Paste Image extensions
      - Foam for VSCode
      - GitHub Pull Requests and Issues
      - GitLens - Git supercharged
      - Jupyter
      - Markdown Notes
      - markdownlink
      - ToDo+
  - Ansible
    - `python3 -m venv .venv && source .venv/bin/activate && pip install --upgrade pip && pip install ansible`
 - Firefox
 - ProWriting Aid
 - BoxDrive (not BoxSync, have to reboot after install)
 - OmniGraffle
 - Microsoft Office Suite: https://portal.office.com/account/
 - Tor Browser
 - Wireshark
 - Duet
 - Etcher
 - Postman
 - imagemagick
 - Microsoft Remote Desktop
 - Burp Suite


## Post Installation Notes:

- `python3` is installed by default on Big Sur, I will try this out before installing `python3` with `brew`.
- I will no longer `pip install` anything outside of a python virtual environment `.venv`
