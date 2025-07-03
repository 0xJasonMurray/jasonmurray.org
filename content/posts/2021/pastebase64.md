---
title: "Automatically convert binary data in the paste buffer to Base64 encoded text with iTerm2"
date: 2021-02-19T13:04:28-06:00
toc: false
images:
categories:
  - tech
tags: 
  - iterm2
  - base64
description: The iTerm2 menu, 'Paste File Base64-Encoded', will automatically convert binary data in the paste buffer to Base64 encoded text.
---

## Overview

Paste binary files from the copy-paste buffer with `iterm2` using the `Edit -> Paste Special -> Paste File Base64-Encoded` menu.  

## Details

Select a file to `paste` on the remote server:

[![Image of ](/images/2021-02-19-13-07-14.png)](/images/2021-02-19-13-07-14.png)

Log into a remote server and edit a file with `vi` (or your favorite editor):

```
jemurray@shell:~$ vi image.jpg
```

[![Image of ](/images/2021-02-19-13-09-03.png)](/images/2021-02-19-13-09-03.png)

Binary data in the paste buffer is automatically converted to Base64 text. Paste the image with `Edit -> Paste Special -> Paste File Base64-Encoded`:

[![Image of ](/images/2021-02-19-13-19-07.png)](/images/2021-02-19-13-19-07.png)

Depending on the size of the file pasting text may take a while.  Pasting text is not the most efficient copy mechanism.  Once complete, save the file: 

[![Image of ](/images/2021-02-19-13-11-12.png)](/images/2021-02-19-13-11-12.png)


The new file is ASCII text:

```
jemurray@shell:~$ file image.jpg
image.jpg: ASCII text
```

Decode (convert) the ASCII file from Base64 back to a `jpg`:

```
jemurray@shell:~$ base64 -d image.jpg > image2.jpg
```

Validate the conversion is successful:

```
jemurray@shell:~$ file image2.jpg
image2.jpg: JPEG image data, JFIF standard 1.01, aspect ratio, density 144x144, segment length 16, Exif Standard: [TIFF image data, big-endian, direntries=5, orientation=upper-left, xresolution=74, yresolution=82, resolutionunit=2], baseline, precision 8, 912x1242, components 3
```

View the file with [`imgcat`](/posts/2021/imgcat/):

[![Image of ](/images/2021-02-19-13-13-22.png)](/images/2021-02-19-13-13-22.png)

