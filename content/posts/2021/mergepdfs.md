---
title: "Merging PDFs with GhostScript on macOS"
date: 2021-01-04T06:48:59-06:00
toc: false
images:
categories:
  - tech
tags: 
  - brew
  - ghostscript
  - gs
---

# Overview

This solution will combine 2 or more PDF's into a single file.  It operates from the command line with nothing more then GhostScript installed from `brew` on macOS (or your favorite package manager on Linux).  


# Details

Install Ghostscript with `brew`:

```
brew install gs
```

Combine the PDF's with GhostScript:

```
gs -q -dNOPAUSE -dBATCH -sDEVICE=pdfwrite -sOutputFile=merged.pdf kc3-2020-obj1.pdf kc3-2020-obj2.pdf
```