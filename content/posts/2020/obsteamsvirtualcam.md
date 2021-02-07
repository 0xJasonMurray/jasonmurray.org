---
title: "Enable OBS Virtual Camera in Microsoft Teams"
date: 2020-09-03T08:32:19-05:00
toc: false
images:
categories:
  - tech
tags: 
  - obs
  - virtualcamera
---

In July 2020, a Microsoft Teams update disabled 3rd party virtual camera support.  Microsoft acknowledged the issue here: https://docs.microsoft.com/en-us/microsoftteams/troubleshoot/teams-on-mac/virtual-camera-doesnt-work-macos

The issue stems from the way Apple codesigns CoreMediaIO 3rd party plugins.

To resolve this problem, the codesign signature from Microsoft Teams must be removed.

Open a terminal and run the following commands:

```
sudo codesign --remove-signature "/Applications/Microsoft Teams.app"
sudo codesign --remove-signature "/Applications/Microsoft Teams.app/Contents/Frameworks/Microsoft Teams Helper.app"
```

The OBS Virtual Camera is visible in Teams:

![Teams OBS Virtual Camera](/images/teamsobscam.png)

This does not come without drawbacks.  Without a codesign signature, the application could be maliciously modified.  Opening up avenues to compromise the integrity of the data transferred within Teams.  Use these instructions at your own risk.
