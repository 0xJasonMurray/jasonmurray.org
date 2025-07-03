---
title: "My backup plan"
date: 2020-06-06T09:48:52-05:00
toc: false
images:
categories:
  - tech
tags: 
  - backups
  - backblaze
  - rsync
  - googlephotos
  - timemachine
  - icloud
---

> Without a solid backup plan, it is not a question of **IF** data will be lost, but **WHEN**.


# Summary

By using a combination of iCloud, BackBlaze, Google Photos, Timemachine, and a few external disks, I have developed a highly reliable backup solution for the most valuable data I own.    

# Requirements

A reliable backup plan has the following requirements. Any exceptions to these rules will eventually result in loss of data:

- All important files **must** be stored in *two* geographically separate locations (local external disks and cloud) at all times.
- The solution **must** run in the background with no user intervention, just as [Ron Popeil](https://en.wikipedia.org/wiki/Ron_Popeil) said, “set it and forget it”.
  

# Overview

By using a variety of software and expanding on the requirements noted earlier, the following diagram is a high level overview of the tools and backup flows.

It may look complicated, but we can summarize it like this:

- Backup everything to [Backblaze](https://secure.backblaze.com/r/009554), iCloud and Timemachine.
- Backup photos to [Backblaze](https://secure.backblaze.com/r/009554), Timemachine, Google Photos, and another external disk.


![backup overview](/images/backup-strategy-flow.png)

# Components

## Backblaze

**[Backblaze](https://secure.backblaze.com/r/009554)** is the most critical component of the backup strategy. Cloud backups are important because:

- Unlike an external drive, backups are stored offsite.  If the house catches fire, floods, or a power surge strikes, having offsite backups stores the data in a geographically diverse location.
- The software is always running in the background looking for changes or new files. It requires no thought or effort to keep the system running.

Note: I chose Backblaze because of their reliable service, excellent customer support, and great [blog](https://www.backblaze.com/blog/blog/). Their engineering team is transparent and posts a lot of excellent information. 

## iCloud

**iCloud** works by storing the original files on the local device and pushes a second copy into the cloud. All other devices attached to the iCloud account have the option to synchronize a copy locally. Since files are stored on local disk, Backblaze and Google Photos are able to push another copy to their respective cloud storage container. This strategy has evolved over the years, from Dropbox and Google to iCloud. Since most of my devices are Apple, the tight integration iCloud offers works exceptionally well. 

## Timemachine

**Timemachine** performs a full backup of the entire drive. In case of complete system failure, recovery is as easy as plugging in the Timemachine drive to a replacement laptop and pressing `control + R`. As long as the drive is plugged in, files are backed up in real time. There are 2 Timemachine drives since my laptop spends half its time and work and half its time at home.

## Google Photos

**Google Photos** is installed on all systems that store or take photos. Google automatically backs up all pictures the instant I take them.

## External photo storage

Two **external drives** are dedicated to my extensive photo collection. One is connected to the laptop when updating photos, the other drive is stored in a fireproof safe. After large changes, the two drives are synchronized using the following `rsync` command:

```
/usr/bin/rsync -azv --stats --progress --delete --exclude ‘.bzvol’ --exclude ‘.Spotlight-V100’ --exclude ‘.DocumentRevisions-V100’ --exclude ‘.TemporaryItems’ --exclude ‘.Trashes’ /Volumes/redstorage/ /Volumes/bluestorage
```

Note: The primary drive is red and the drive in the safe is blue.

# Too complicated

Yes, it might be complicated, but I error on the side of caution.

If you could only pick one option, I would highly recommend using a cloud backup service such as Backblaze or Carbonite. It runs in the background, moves data offsite, and backs up all files instantly without having to think about it.