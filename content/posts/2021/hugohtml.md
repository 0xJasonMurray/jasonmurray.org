---
title: "Render raw html in HUGO markdown files"
date: 2021-04-02T06:32:30-05:00
toc: false
images:
categories:
  - tech
tags: 
  - hugo
  - html
  - markdown
description: Render raw html in HUGO markdown files
---

By default Hugo will not render embedded `html` in markdown files.  To enable, add the following lines to `config.toml`:

```
[markup.goldmark.renderer]
  unsafe = true
```

Entering the raw `html`:

```
<h1>hello world!</h1>
```

Renders:

<h1>hello world!</h1>