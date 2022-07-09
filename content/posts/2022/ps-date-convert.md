---
title: "Using PowerShell to parse Office 365 TimeStamps in JSON format"
date: 2022-07-09T15:15:27-05:00
draft: false
toc: false
images:
categories:
  - tech
tags: 
  - powershell
description: Using PowerShell to convert Office 365 timestamps in JSON format to a human readable format.
---

Given a JSON file with TimeStamp values from a Microsoft Office 365 Search History dump:

```
PS /Users/jemurray> Get-Content ./ahg.txt -Raw
{"SearchHistoryItems":[{"DisplayText":"test1","TimeStamp":637927286902301816},{"DisplayText":"test2","TimeStamp":637927286812301816},{"DisplayText":"test3","TimeStamp":637927120382301816},{"DisplayText":"test4","TimeStamp":637922768672301816},
....
```

Convert the TimeStamp values to a human readable format:


```
PS /Users/jemurray> Get-Content ./ahg.txt -Raw | ConvertFrom-Json | ForEach-Object {$_.PsObject.Properties.Value.TimeStamp} | Get-Date

Wednesday, July 6, 2022 6:24:50 PM
Wednesday, July 6, 2022 6:24:41 PM
Wednesday, July 6, 2022 1:47:18 PM
Friday, July 1, 2022 12:54:27 PM
Thursday, June 30, 2022 9:43:30 PM
Thursday, June 30, 2022 1:47:13 PM
Wednesday, June 29, 2022 8:30:43 PM
Wednesday, June 29, 2022 8:28:10 PM
...
```