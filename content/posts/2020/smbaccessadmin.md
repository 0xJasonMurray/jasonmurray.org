---
title: "SMB drive access problem within Administrator PowerShell terminal"
date: 2020-06-05T10:11:12-05:00
toc: false
images:
categories:
  - tect
tags: 
  - smb
  - windows10
---

# Summary

Mounting a remote SMB drive under a regular user, then trying to access it via the Administrator account within PowerShell results in the following error:

```
dir : Cannot find path '\\storage1.example.com\dir' because it does not exist.
```

To resolve this problem, use the `net use` command under an Administrator PowerShell terminal.

# Details

SMB drive mounted via the GUI under the local user account:

![smb mount](/images/smbguimount.png)

Attempting to access the share under the Administrator PowerShell user generates the following error:

```
(base) PS C:\Users\jemurray\Documents\JasonMurrayPerfTesting> dir \\storage1.example.com\dir
dir : Cannot find path '\\storage1.example.com\dir' because it does not exist.
At line:1 char:1
+ dir \\storage1.example.com\dir
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : ObjectNotFound: (\\storage1.example.com\dir:String) [Get-ChildItem], ItemNotFoundException
    + FullyQualifiedErrorId : PathNotFound,Microsoft.PowerShell.Commands.GetChildItemCommand
```


To resolve this issue, first unmount the drive.  Then remount it under the Administrator PowerShell terminal using the `net use` command:

```
(base) PS C:\Users\jemurray\Documents\JasonMurrayPerfTesting> net use Z: "\\storage1.example.com\dir"
The password or user name is invalid for \\storage1.example.com\dir.

Enter the user name for 'storage1.example.com': domain\jemurray
Enter the password for storage1.example.com:
The command completed successfully.
```

Confirm access functions as expected:

```
(base) PS C:\Users\jemurray\Documents\JasonMurrayPerfTesting> dir z:\


    Directory: z:\


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----         6/5/2020   9:52 AM                Active
d-----        5/22/2020   3:26 PM                Archive
```


