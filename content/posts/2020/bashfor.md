---
title: "Bash one-liner to generate markdown images"
date: 2020-07-12T12:18:50-05:00
toc: false
images:
categories:
  - tech
tags: 
  - bash
  - simplescripts
  - cli
---

I like these one line scripts that save a whole bunch of time.  I wish the entire world knew the power of scripting from the command line!

A simple `bash` one(ish)-liner to generate the `markdown` syntax to display an entire directory of photos:

```
jemurray@mbp-2019:~/www/static/images/faucet $ for i in `ls -1` ; do echo " "; echo $i; echo "\![faucet](/images/faucet/$i)" | sed -e 's/^\\//g'; done

IMG_0385.JPG
![faucet](/images/faucet/IMG_0385.JPG)

IMG_0616.JPG
![faucet](/images/faucet/IMG_0616.JPG)

IMG_0968.JPG
![faucet](/images/faucet/IMG_0968.JPG)

IMG_0995.JPG
![faucet](/images/faucet/IMG_0995.JPG)
```