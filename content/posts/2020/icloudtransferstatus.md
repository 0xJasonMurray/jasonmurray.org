---
title: "How to check iCloud Drive file transfer status on macOS"
date: 2020-07-15T14:59:56-05:00
toc: false
images:
categories:
  - tech
tags:
  - icloud
---

iCloud Drive is used to synchronize files across iDevice systems. However, it is not obvious what the status of the file synchronization is.  Third-party cloud vendors such as Box, Dropbox, and Google Drive all have an icon in the menu bar which displays the sync status.  

With iCloud Drive you need to open finder and enable the Status Bar. By default there is no iCloud drive synchronization indicator:

![finder](/images/findernoicloudstatus.png)

To enable iCloud synchronization status in the finder window, select View -> Show Status Bar

![finder](/images/finderenableicloudstatus.png)

The upload / download transfer progress is now displayed at the bottom of the finder window:

![finder](/images/finderwithicloudstatus.png)

In addition to the status bar, each file may have an indicator after the file name or an arrow in the file size.  For example, the pie chart indicates a transfer is in progress and the arrow indicates an upload:

![finder](/images/finderclock.png)

To see the iCloud status of each file all the time, right click the columns and select iCloud Status:

![finder](/images/findericloudcolumn.png)

Another way to check if iCloud is transferring files is to open `activity monitor` and look at the processes performing large file transfers.  For example, `nsurlsessiond` is responsible for iCloud file synchronization activities.  If it is sending a lot of data, there is a high probability an iCloud file transfer is taking place:

![finder](/images/activitymonitornetwork.png)

