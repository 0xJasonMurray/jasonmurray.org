---
title: "Searching websites that don't have a native search function"
date: 2020-11-23T08:30:09-06:00
toc: false
images:
categories:
  - tech
tags: 
  - search
  - google
  - duckduckgo
  - site
  - static
---


Statically generated websites, like this one, don't rely on server side processing.   This makes it more difficult to include a native search function.  I know there are tools to enable search, but the entire point of building this site with a static generator is to keep it simple.  I don't want deal with maintaining additional software or building non-native libraries.

Fortunately, there is an easy alternative.  The big search engines, [Google](https://support.google.com/websearch/answer/2466433?hl=en) and [Duck Duck Go](https://help.duckduckgo.com/duckduckgo-help-pages/results/syntax/) have the `site:` search filter.

By adding the `site:` option, results are limited to only the site specified.   For example, to search for `serial console` information on the website `jasonmurray.org` using the following search string: `site:jasonmurray.org serial console`.   

Google search results:

![](/images/2020-11-23-08-30-19.png)

Duck Duck Go search results:

![](/images/2020-11-23-08-35-50.png)