---
title: "Using Pandoc to convert Hugo markdown with inline images"
date: 2020-12-26T16:05:29-06:00
toc: false
images:
categories:
  - tech
tags: 
  - pandoc
---

# Problem

I write a lot of documentation within the [Hugo](https://gohugo.io/) framework for publishing on my [personal website](https://jasonmurray.org) (this site).  On occasion, I prefer to export a page to a standalone file without the HTML headers and footers Hugo applies as the site is generated.  

To convert Hugo `markdown` files I use `pandoc`.  If the `markdown` files contain explicit image links like this `![](/images/2020-12-26-15-31-02.png)` `pandoc` will not find the images:

```
jemurray@mbp-2019:~/Documents/kringle2020/writeups $ pandoc ../../www-personal/current/jasonmurray.org/content/posts/kc3-2020-obj9.md  -o obj9.docx -f markdown -t docx --resource-path=../../www-personal/current/jasonmurray.org/static
[WARNING] Could not fetch resource '/images/2020-12-26-15-31-02.png': PandocIOError "/images/2020-12-26-15-31-02.png" /images/2020-12-26-15-31-02.png: openBinaryFile: does not exist (No such file or directory)
```

# Solution

To resolve the explicit image path issue, I preprocess the `markdown` files with the following `sed` search-and-replace which replaces the explicit path with a relative path: 

```
sed 's/(\/images\//(/g'
```

The full `pandoc` and `sed` command looks like this (using a Kringlecon3 writeup as my example):

```
jemurray@mbp-2019:~/Documents/kringle2020/writeups $ cat ../../www-personal/current/jasonmurray.org/content/posts/kc3-2020-obj9.md | sed 's/(\/images\//(/g' | pandoc -o obj9.docx -f markdown -t docx --resource-path=../../www-personal/current/jasonmurray.org/static/images/
```

Example `docx` output with inline images:

![](/images/2020-12-26-16-21-58.png)



I suspect `pandoc` has an option to do this without the `sed` prefilter, but I could not find it?  If there is, please comment below.