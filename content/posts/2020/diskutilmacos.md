---
title: "Is that the right USB drive?"
date: 2020-08-30T21:03:06-05:00
toc: false
images:
categories:
  - tech
tags: 
  - diskutil
---

Determining which local disk is being operated on is critically important to prevent data loss or corruption. One wrong option to the `dd` command has the potential to wipe out a local hard drive.


Display the internal system drives by running `diskutil list` with no other drives attached.  These are disks you don't want to mess with:

```
jemurray@mbp-2019:~ $ diskutil list
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *500.3 GB   disk0
   1:                        EFI EFI                     314.6 MB   disk0s1
   2:                 Apple_APFS Container disk1         500.0 GB   disk0s2

/dev/disk1 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +500.0 GB   disk1
                                 Physical Store disk0s2
   1:                APFS Volume Macintosh HD - Data     436.8 GB   disk1s1
   2:                APFS Volume Preboot                 82.7 MB    disk1s2
   3:                APFS Volume Recovery                528.5 MB   disk1s3
   4:                APFS Volume VM                      9.7 GB     disk1s4
   5:                APFS Volume Macintosh HD            11.0 GB    disk1s5
```

Insert a USB drive and run `diskutil list` again.  Notice how the new drive shows up at the end of the list.   Most of the time new disks will be `/dev/disk2` or higher.   However, **don't** rely on that.  This is why we validate every single time before running commands which have the potential to destroy data without asking.  Remember what your grandpa told you, "measure twice, cut once".    

```
jemurray@mbp-2019:~ $ diskutil list
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *500.3 GB   disk0
   1:                        EFI EFI                     314.6 MB   disk0s1
   2:                 Apple_APFS Container disk1         500.0 GB   disk0s2

/dev/disk1 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +500.0 GB   disk1
                                 Physical Store disk0s2
   1:                APFS Volume Macintosh HD - Data     436.8 GB   disk1s1
   2:                APFS Volume Preboot                 82.7 MB    disk1s2
   3:                APFS Volume Recovery                528.5 MB   disk1s3
   4:                APFS Volume VM                      9.7 GB     disk1s4
   5:                APFS Volume Macintosh HD            11.0 GB    disk1s5

/dev/disk2 (external, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:                            pS-Toolkit             *7.7 GB     disk2
```

`diskutil info /dev/disk2` will display additional information to validate we are operating on the proper USB drive if multiple drives are attached at the same time:

```
jemurray@mbp-2019:~ $ diskutil info /dev/disk2
   Device Identifier:         disk2
   Device Node:               /dev/disk2
   Whole:                     Yes
   Part of Whole:             disk2
   Device / Media Name:       DataTraveler 3.0

   Volume Name:               pS-Toolkit
   Mounted:                   Yes
   Mount Point:               /Volumes/pS-Toolkit

   Content (IOContent):       None
   File System Personality:   ISO Rockridge
   Type (Bundle):             cd9660
   Name (User Visible):       ISO 9660 (Rockridge)

   OS Can Be Installed:       No
   Media Type:                Generic
   Protocol:                  USB
   SMART Status:              Not Supported

   Disk Size:                 7.7 GB (7736072192 Bytes) (exactly 15109516 512-Byte-Units)
   Device Block Size:         512 Bytes

   Volume Total Space:        1.8 GB (1766012928 Bytes) (exactly 3449244 512-Byte-Units)
   Volume Used Space:         1.8 GB (1766012928 Bytes) (exactly 3449244 512-Byte-Units) (100.0%)
   Volume Free Space:         0 B (0 Bytes) (exactly 0 512-Byte-Units) (0.0%)
   Allocation Block Size:     2048 Bytes

   Read-Only Media:           No
   Read-Only Volume:          Yes

   Device Location:           External
   Removable Media:           Removable
   Media Removal:             Software-Activated

   Solid State:               Info not available
   Virtual:                   No
```

Relax, we are now assured that running `sudo gdd if=/dev/zero of=/dev/disk3 status=progress` will not corrupt the main OS disk:

```
jemurray@mbp-2019:~ $ sudo gdd if=/dev/zero of=/dev/disk3 status=progress
33698304 bytes (34 MB, 32 MiB) copied, 13 s, 2.6 MB/s
```