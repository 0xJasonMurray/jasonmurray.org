---
title: "Pasting images into Hugo markdown with vsCode on macOS"
date: 2020-10-24T10:54:40-05:00
toc: false
images:
categories:
  - tech
tags: 
  - vscode
---

# Overview

Paste images directly into markdown code with the vsCode [Paste Image plugin](https://marketplace.visualstudio.com/items?itemName=mushan.vscode-paste-image). 

Before this plugin, adding images to Hugo markdown code required:

- Capturing the picture
- Move the picture into the static directory
- Rename the picture
- Add the markdown code with the proper picture name. 

After the Paste Image plugin:

- Capture the picture
- Right click image and save to the clipboard
- Paste image into markdown (everything else is automatic)

# Details

My Hugo directory structure is setup like this:

- `{projectRoot}` = The main Hugo directory
- `{projectRoot}/static/images` = Image files

When editing content in Hugo, I open the `{projectRoot}` folder.  This automatically sets the `${projectRoot}` variable used in the settings:

![](/images/2020-10-22-15-39-54.png)


Install the [Paste Image vsCode Plugin](https://marketplace.visualstudio.com/items?itemName=mushan.vscode-paste-image):

![](/images/2020-10-22-15-30-44.png)

Edit the `Paste Image` settings (Code -> Preferences -> Settings -> Extentions -> Paste Image Configuration):

![](/images/2020-10-22-15-33-00.png)

Capture an image with `COMMAND + SHIFT + 4` and save it to the `clipboard`.

Use `COMMAND + OPTION + v` (on macOS) to paste images.  It will automatically save the image to the `.../static/images` directory and put the proper `/images/imagename.ext` in the markdown file:

![](/images/2020-10-22-15-41-48.png)

