---
title: "git broken after 10.14.x to 10.15.x macOS upgrade"
date: 2020-06-25T20:56:46-05:00
toc: false
images:
categories:
  - tech
tags: 
  - git
  - macos
  - upgrade
  - xcode
---

After upgrading macOS from 10.14.x to 10.15.x `git` no longer worked:

```
jemurray@mbp-2019:~ $ git
xcrun: error: invalid active developer path (/Library/Developer/CommandLineTools), missing xcrun at: /Library/Developer/CommandLineTools/usr/bin/xcrun
```

Tried this:

```
jemurray@mbp-2019:~ $ xcode-select --install
xcode-select: note: install requested for command line developer tools
```

Which raised the following error:

![xcode-select error](/images/xcodeinstallfail.png)



Tried this, with no success:

```
jemurray@mbp-2019:~ $ sudo xcode-select --reset
```

Manually downloaded the latest xcode from the App Store:

![xcode download app store](/images/xcodedownload.png)

Manually downloading the software is getting us closer:

```
jemurray@mbp-2019:~ $ git


You have not agreed to the Xcode license agreements. You must agree to both license agreements below in order to use Xcode.

Hit the Return key to view the license agreements at '/Applications/Xcode.app/Contents/Resources/English.lproj/License.rtf'
```

Agree to the license:

```
jemurray@mbp-2019:~ $ sudo xcodebuild -license
...
Agree
```

Success:

```
jemurray@mbp-2019:~ $ git
usage: git [--version] [--help] [-C <path>] [-c <name>=<value>]
           [--exec-path[=<path>]] [--html-path] [--man-path] [--info-path]
           [-p | --paginate | -P | --no-pager] [--no-replace-objects] [--bare]
           [--git-dir=<path>] [--work-tree=<path>] [--namespace=<name>]
           <command> [<args>]
```