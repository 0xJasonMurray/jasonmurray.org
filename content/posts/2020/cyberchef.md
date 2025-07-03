---
title: "Cyberchef, the swiss army knife of converting, analyzing, and decoding information"
date: 2020-12-20T18:32:18-06:00
toc: false
images:
categories:
  - tech
tags: 
  - cyberchef
---

# Overview

[CyberChef](https://gchq.github.io/CyberChef/) is a simple, intuitive web app for analysing and decoding data without having to deal with complex tools or programming languages. 

There are around 200 useful operations in CyberChef for anyone working on anything vaguely Internet-related, whether you just want to convert a timestamp to a different format, decompress gzipped data, create a SHA3 hash, or parse an X.509 certificate to find out who issued itCyberChef encourages both technical and non-technical people to explore data formats, encryption and compression.

I find the CLI to be quicker for many of these operations, but for infrequently used commands with complex options, this is a time saver.  

# How

Drag an `operation` from the tool bar on the left side into `recipe` column.   Multiple `operations` can be linked together, which are processed top-down.  Next, add values into the `input` box in the top-right.  The results are displayed in the `output` box on the bottom-left.



# Examples

## Examining a TLS/SSL Certificate:

I never remember this `openssl` command: `openssl x509 -in cert.pem -text -noout`.  Luckily, I no longer have to:

![](/images/2020-12-20-18-53-11.png)

## Convert a Unix time stamp to human readable:

![](/images/2020-12-20-18-41-18.png)

## Decode BASE64 text

![](/images/2020-12-20-18-58-12.png)

## Multiple Operations in a Recipe

Multiple operations can be linked together within a single recipe.  Processing is from top-down.

![](/images/2020-12-20-19-08-13.png)