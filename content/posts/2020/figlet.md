---
title: "Big ASCII letters with figlet"
date: 2020-09-14T16:00:32-05:00
toc: false
images:
categories:
  - tech
tags: 
  - figlet
  - ascii
---

I am an old school nerd from a time when ASCII art was king.  Printers were dot matrix. Tractor-feed continuos paper made great banners.  [The Print Shop](https://en.wikipedia.org/wiki/The_Print_Shop) was high tech publishing software.

When ASCII nostalgia strikes, [figlet](http://www.figlet.org/) is here to help.

```
jemurray@shell:~$ figlet Hello World!
 _   _      _ _        __        __         _     _ _
| | | | ___| | | ___   \ \      / /__  _ __| | __| | |
| |_| |/ _ \ | |/ _ \   \ \ /\ / / _ \| '__| |/ _` | |
|  _  |  __/ | | (_) |   \ V  V / (_) | |  | | (_| |_|
|_| |_|\___|_|_|\___/     \_/\_/ \___/|_|  |_|\__,_(_)
```

How about really old school with the `#` 1980's pixel:

```
jemurray@shell:~$ figlet -t -f banner Old School Nerd!
#######                   #####                                        #     #                      ###
#     # #      #####     #     #  ####  #    #  ####   ####  #         ##    # ###### #####  #####  ###
#     # #      #    #    #       #    # #    # #    # #    # #         # #   # #      #    # #    # ###
#     # #      #    #     #####  #      ###### #    # #    # #         #  #  # #####  #    # #    #  #
#     # #      #    #          # #      #    # #    # #    # #         #   # # #      #####  #    #
#     # #      #    #    #     # #    # #    # #    # #    # #         #    ## #      #   #  #    # ###
####### ###### #####      #####   ####  #    #  ####   ####  ######    #     # ###### #    # #####  ###
```

