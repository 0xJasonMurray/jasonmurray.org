---
title: "Adding plausable.io analytics to Hugo"
date: 2021-03-31T14:03:54-05:00
draft: true
toc: false
images:
categories:
  - tech
tags: 
  - hugo
  - plausable
  - git
description: Adding plausable.io analytics to Hugo
---



```
cd themes
git submodule add https://github.com/divinerites/plausible-hugo.git
```

Edit the `theme` variable in the `config.toml` file:

```
theme = ["hermit", "plausible-hugo"]
```


Add the follow line to `layouts/partials/extra-head.html`:

```
{{ partial "plausible_head.html" . }}
```