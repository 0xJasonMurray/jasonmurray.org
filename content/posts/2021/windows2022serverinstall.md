---
title: "Installing Windows 2022 Server in a Lab Environment"
date: 2021-12-01T09:00:52-06:00
toc: false
images:
categories:
  - tech
tags: 
  - windows2022
description: Instructions for installing Windows 2022 Server under VMware ESXi in a home lab environment.
---

Download a free and legal copy of Windows Server 2022 (evaluation version) from https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022

[![Image of ](/images/2021-12-01-09-06-52.png)](/images/2021-12-01-09-06-52.png)

Configure the VMware virtual machine, mount the `.iso` install image, start the VM, and log into the console (outside the scope of this document):

[![Image of ](/images/2021-12-01-12-26-27.png)](/images/2021-12-01-12-26-27.png)

Select the language:

[![Image of ](/images/2021-12-01-09-01-15.png)](/images/2021-12-01-09-01-15.png)

Select "Install Now":

[![Image of ](/images/2021-12-01-09-01-38.png)](/images/2021-12-01-09-01-38.png)

The `Windows 2002 Server Standard Desktop Edition` is selected to simplify management in a home lab environment:

[![Image of ](/images/2021-12-01-09-05-18.png)](/images/2021-12-01-09-05-18.png)

Accept the license:

[![Image of ](/images/2021-12-01-09-08-22.png)](/images/2021-12-01-09-08-22.png)

Perform a custom install in order to select the appropriate storage settings:

[![Image of ](/images/2021-12-01-09-09-56.png)](/images/2021-12-01-09-09-56.png)

Select the appropriate disk for installation:

[![Image of ](/images/2021-12-01-09-13-16.png)](/images/2021-12-01-09-13-16.png)


It takes about 5-10 minutes for the OS to install:

[![Image of ](/images/2021-12-01-09-15-37.png)](/images/2021-12-01-09-15-37.png)


Still setting up:

[![Image of ](/images/2021-12-01-09-20-31.png)](/images/2021-12-01-09-20-31.png)

Set the `administrator` password:

[![Image of ](/images/2021-12-01-09-23-05.png)](/images/2021-12-01-09-23-05.png)

To access the console in VMware, send ctrl-alt-del from the Settings -> Guest OS -> Send Keys menu:

[![Image of ](/images/2021-12-01-09-25-38.png)](/images/2021-12-01-09-25-38.png)

First thing, check for system updates in Settings -> Updates & Security -> Windows Update:

[![Image of ](/images/2021-12-01-09-54-36.png)](/images/2021-12-01-09-54-36.png)

I prefer to manage lab devices from Remote Desktop. From Settings -> System -> Remote Desktop, enable remote desktop:

[![Image of ](/images/2021-12-01-09-29-12.png)](/images/2021-12-01-09-29-12.png)

Allow Remote Desktop:

[![Image of ](/images/2021-12-01-09-28-15.png)](/images/2021-12-01-09-28-15.png)

Give the server a meaningful name. From Settings -> System -> About, Rename this PC:

[![Image of ](/images/2021-12-01-09-36-54.png)](/images/2021-12-01-09-36-54.png)

Restart required after name change:

[![Image of ](/images/2021-12-01-09-37-32.png)](/images/2021-12-01-09-37-32.png)

Even in a lab environment, it is important to have accurate timestamps for troubleshooting and testing. Set the `timezone` in Settings -> Time & Language -> Date & Time:

[![Image of ](/images/2021-12-01-10-35-34.png)](/images/2021-12-01-10-35-34.png)

Try to remote desktop:

[![Image of ](/images/2021-12-01-09-42-35.png)](/images/2021-12-01-09-42-35.png)

Full desktop:

[![Image of ](/images/2021-12-01-09-51-35.png)](/images/2021-12-01-09-51-35.png)


Even though this is a lab environment, it's best practice to to use a non-privileged account for day-to-day administration.  Using `powershell`, create a regular user account:

```text
$Password = Read-Host -AsSecureString
New-LocalUser -Name "jemurray" -Password $Password -FullName "Jason Murray"
Add-LocalGroupMember -Group "Remote Desktop Users" -Member jemurray
```

Example output: 

```
PS C:\Users\Administrator> $Password = Read-Host -AsSecureString
*********
PS C:\Users\Administrator> New-LocalUser -Name "jemurray" -Password $Password -FullName "Jason Murray"

Name     Enabled Description
----     ------- -----------
jemurray True

PS C:\Users\Administrator> Add-LocalGroupMember -Group "Remote Desktop Users" -Member jemurray
```