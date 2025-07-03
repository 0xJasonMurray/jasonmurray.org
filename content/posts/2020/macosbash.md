---
title: "Suppressing the bash warning in macOS 10.15"
date: 2020-08-22T13:01:07-05:00
toc: false
images:
categories:
  - tech
tags: 
  - bash
  - zsh
  - macos
---

After upgrading to macOS 10.15, this warning message is displayed when opening or creating a terminal shell:

```
jemurray@mbp-2019:~ $ exec bash

The default interactive shell is now zsh.
To update your account to use zsh, please run `chsh -s /bin/zsh`.
For more details, please visit https://support.apple.com/kb/HT208050.
```

Add the following environment variable to the `.bash_profile` to permanently suppress the warning: 

```
echo "export BASH_SILENCE_DEPRECATION_WARNING=1" >> .bash_profile
```

The warning is gone:

```
jemurray@mbp-2019:~ $ exec bash
jemurray@mbp-2019:~ $
```