---
title: "Create an online presentations with markdown and Remark"
date: 2021-03-08T06:40:52-06:00
toc: false
images:
categories:
  - tech
tags: 
  - remark
  - vscode
  - liveserver
  - hugo
description: Quickly build a presentation in markdown and vscode, then publish online with Hugo.  
---

## Overview

Create a slide show presentation written purely in markdown and displayed in a standard web browser using [Remark](https://github.com/gnab/remark).

By using my existing Hugo website framework, I can publish slides for public consumption:

[![Image of markdown generated slide show](/images/2021-03-08-20-32-41.png)](/images/2021-03-08-20-32-41.png)
## Details

Create a folder to store presentations.  For this example, I will use a directory under the Hugo framework to make the presentation publicly available:

```
jemurray@jasons-mbp:~/Documents/www-personal/current/jasonmurray.org $ mkdir static/presentationdev
jemurray@jasons-mbp:~/Documents/www-personal/current/jasonmurray.org $
```

In the newly created directory, add an `index.html` file with the Remark scripts to load the slide show content:

```
<!DOCTYPE html>
<html>
  <head>
    <title>Title</title>
    <meta charset="utf-8">
    <style>
      @import url(https://fonts.googleapis.com/css?family=Yanone+Kaffeesatz);
      @import url(https://fonts.googleapis.com/css?family=Droid+Serif:400,700,400italic);
      @import url(https://fonts.googleapis.com/css?family=Ubuntu+Mono:400,700,400italic);
      body { font-family: 'Droid Serif'; }
      h1, h2, h3 {
        font-family: 'Yanone Kaffeesatz';
        font-weight: normal;
      }
      .remark-code, .remark-inline-code { font-family: 'Ubuntu Mono'; }
    </style>
  </head>
  <body>
  <script src="https://remarkjs.com/downloads/remark-latest.min.js"></script>
  <script>
    var slideshow = remark.create({
      highlightStyle: 'monokai',
      sourceUrl: 'content.md',
      slideNumberFormat: ''
    });
  </script>
  </body>
</html>
```

Create the file `content.md` which contains the content of the slide show presentation:

```
class: center, middle

# Using Markdown to Create Slides

---

# Agenda

1. Introduction
2. Deep-dive
3. ...

---

# Introduction

Lists:

- foo
- bar
- baz

---

# Display Code Blocks

```terminal
#!/usr/bin/env python3

print("Hello World!")
```

While creating content, I use the `vscode` extension `Live Server` to display and automatically reload the slide deck after each save.  Start the web server by clicking the `Go Live` button:

[![Image of ](/images/2021-03-08-20-54-47.png)](/images/2021-03-08-20-54-47.png)

While editing or displying private presentations, use the `Live Server` locally at the URL: `http://127.0.0.1:5500/static/presentationdev/`:

[![Image of ](/images/2021-03-08-20-58-57.png)](/images/2021-03-08-20-58-57.png)

For public presentations, I post the content in the static directory of my [existing Hugo website](/posts/2020/websiteflow/):

[![Image of markdown generated slide show](/images/2021-03-08-20-32-41.png)](/images/2021-03-08-20-32-41.png)



Example site: [https://jasonmurray.org/presentationdev/](https://jasonmurray.org/presentationdev/) - Use the arrow keys to move between slides.