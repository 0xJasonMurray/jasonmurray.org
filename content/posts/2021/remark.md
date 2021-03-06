---
title: "Creating slide show presentations with Remark"
date: 2021-03-07T06:40:52-06:00
draft: true
toc: false
images:
categories:
  - tech
tags: 
  - remark
description:
---

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


```
class: center, middle

# How to Create Slides with Markdown and Remark

---

# Agenda

1. Introduction
2. Deep-dive
3. ...

---

# Introduction

- foo
- bar
- baz
```

Example site: https://jasonmurray.org/presentations/#1