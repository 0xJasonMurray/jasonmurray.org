---
title: "Copying Excel formulas without offset changes"
date: 2020-08-25T10:37:29-05:00
toc: false
images:
categories:
  - tech
tags: 
  - excel
---

When copying cells with formulas in Excel, the system tries to determine the proper offset of the pasted formulas.  In this example, I created references to cells in other sheets.  The two sheets are layed out identically. This way when the summary page is built I would only have to copy the reference formulas and change the sheet location.

However, when copying a reference formula from cell A2 to A19, Excel changed the offset in the formula from B128 to B145:

- Original: =macOS!B128
- Pasted: =macOS!B145

I don't want the reference cell to change, what I want to happen is this:

- Original: =macOS!B128
- Pasted: =macOS!B128

Unless I am missing something and my Google skills are lacking, there is no obvious way to accomplish this task with a "paste special" option.

The "quick" way to copy a formula without Excel changing the offset, is to temporarily disable the formula with the search-and-replace tool:

![excel search and replace](/images/excelsearchandreplaceformula.png)

After the formulas ='s are replaced with \\'s, copy and paste the required cells, then rerun the search-and-replace to to convert the \\'s back to ='s:


![excel search and replace](/images/excelsearchandreplaceundone.png)