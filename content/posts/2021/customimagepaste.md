---
title: "Make a clickable image link in vscode with the Paste Image extension"
date: 2021-02-22T08:01:41-06:00
toc: false
images:
categories:
  - tech
tags: 
  - vscode
  - pasteimage
  - markdown
  - image
description: Create clickable images using the vscode extension Paste Image.  By default images are not clickable unless the pasteImage.insertPattern is modified.  
---

## Overview

The vscode extension, `Paste Image` takes an image from the `clipboard`, inserts a `markdown` or `asciidoc` link within the document, then copies the image into the appropriate directory. The default `Paste Image` settings do not create a clickable link to view the original image. This may be a problem if the display source resizes the content, making it difficult to read.
## Details



I use `Paste Image` to insert images into the `markdown` used to create this website.  The following `.vscode/settings.json` lines are the stock plugin settings:

```
{
    "pasteImage.insertPattern": "${imageSyntaxPrefix}/${imageFileName}${imageSyntaxSuffix}",
    "pasteImage.path": "${projectRoot}"
}
```

Modify or create the following options `pasteImage.insertPattern` and `pasteImage.path` in the `vscode` configuration file `.vscode/settings.json`.  In this example, I will enable clickable images for use in the [Hugo](https://gohugo.io/) static website generation framework by wrapping a clickable link `[]()` around an image tag `![]()` and setting the proper path location:

```
{
    "pasteImage.insertPattern": "[![Image of ](/images/${imageFileName})](/images/${imageFileName})",
    "pasteImage.path": "${projectRoot}/static/images/"
}
```

Taking a quick screen shot, and pressing `<option> + <command> + v` inserts this code:

```
[![Image of a test clickable image in Hugo](/images/2021-02-13-18-14-59.png)](/images/2021-02-13-18-14-59.png)
```

Which creates the image below.  Try clicking it:

[![Image of a test clickable image in Hugo](/images/2021-02-13-18-14-59.png)](/images/2021-02-13-18-14-59.png)