---
title: "Adding plausible.io analytics to Hugo"
date: 2021-03-31T14:03:54-05:00
toc: false
images:
categories:
  - tech
tags: 
  - hugo
  - plausible
  - git
description: Adding plausible.io analytics to Hugo
---

## Overview

This year (2021) I removed Google Analytics and [Disqus](/posts/2021/disqusprivacy/) from my Hugo managed website to enhance the privacy of visitors. Site statistics is not a requirement, I am not selling anything or making an attempt to optimize readership. However, it is nice to know what people are interested in reading. At some point I may try to tailor content that is more interesting to my readers instead of whatevers top of mind for the day.

This post details what it takes to configure and install the hosted version of [Plausible](https://plausible.io/) within the [Hugo](https://gohugo.io/). I thought about [self-hosting](https://plausible.io/self-hosted-web-analytics), but I would rather support quality software.

## Details

As noted above, Hugo is the website content management framework.

Plausible [recommends](https://plausible.io/docs/hugo-integration) using the [plausible-hugo](https://github.com/divinerites/plausible-hugo/blob/master/README.md) Hugo theme. For those managing themes with `git`, clone the module with the `submodule` option:

```text
cd themes
git submodule add https://github.com/divinerites/plausible-hugo.git
```

Add `plausible-hugo` to the `theme` variable in the `config.toml` file:

```text
theme = ["hermit", "plausible-hugo"]
```


Enable analytics on all pages, overriding the `Hermit` theme `<head>` section by adding (or creating) the following line to `layouts/partials/extra-head.html`:

```text
{{ partial "plausible_head.html" . }}
```

While loading speed is not an issue for this site, Plausible is the main contributor of load time:

[![Image of ](/images/2021-03-31-17-28-57.png)](/images/2021-03-31-17-28-57.png)

I enable public statistics, browse to https://plausible.io/jasonmurray.org

[![Image of ](/images/2021-03-31-17-43-19.png)](/images/2021-03-31-17-43-19.png)