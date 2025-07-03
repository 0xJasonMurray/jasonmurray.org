---
title: "Display images through an iTerm2 terminal session"
date: 2021-01-12T16:29:28-06:00
toc: false
images:
categories:
  - tech
tags: 
  - iterm2
  - imgcat
  - ssh
---

# Overview

[iTerm2](https://iterm2.com/documentation-images.html) can display images within a terminal session without the need for a external display program.  Even over a `ssh` session!

# Details

You must be using `iTerm2`.

Download `imgcat`:

```
wget https://iterm2.com/utilities/imgcat
```

Make `imgcat` executable:

```
chmod 755 ./imgcat
```

Display an image from the terminal by executing:

```
./imgcat Documents/jason-head.jpg
```

The first run will display this warning:

![](/images/2021-01-12-16-29-38.png)

Successfully displaying an image from a terminal session:

![](/images/2021-01-12-16-43-22.png)

This even works over an `ssh` session:

![](/images/2021-01-12-16-41-43.png)

Right-click to save image (even from remote):

![](/images/2021-01-12-16-59-03.png)