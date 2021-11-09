---
title: "Preparing to ThreatHunt: Installing and Configuring Sysmon on Windows 10"
date: 2021-10-29T15:53:43-05:00
toc: false
images:
categories:
  - tech
tags: 
  - windows10
  - sysmon
  - security
  - microsoft
  - splunk
description: In this article, we will walk through installing and configuring Sysmon on Windows 10. Using a modified copy of SwiftOnSecurity's excellent base configuration.
---

## Overview

Sysmon is a free Windows system service that gathers and logs telemetry information to the Windows event log. For security professionals, it provides detailed information about process creations, network connections, and changes to files which can be used to identify nefarious activities by potential threat actors.

In this article, we will walk through installing and configuring Sysmon on Windows 10. Using a modified copy of [SwiftOnSecurity](https://github.com/SwiftOnSecurity/sysmon-config)'s excellent base configuration.

## Details

Browse to the [SwiftOnSecurity](https://github.com/SwiftOnSecurity/sysmon-config) GitHub page and clone the `sysmon-config`  repository by clicking the `Fork` button in the upper right corner. In the next few steps we will modify the configuration for our specific needs:

[![Image of forking SwiftOnSecurity sysmon-config](/images/2021-10-29-15-57-25.png)](/images/2021-10-29-15-57-25.png)


Once the fork is complete, clone the repository on a local machine:

```text
git clone git@github.com:0xJasonMurray/sysmon-config.git
```

Output:

```
jemurray@phalanges:~/Documents/security-configs $ git clone git@github.com:0xJasonMurray/sysmon-config.git
Cloning into 'sysmon-config'...
remote: Enumerating objects: 489, done.
remote: Counting objects: 100% (52/52), done.
remote: Compressing objects: 100% (37/37), done.
remote: Total 489 (delta 29), reused 31 (delta 15), pack-reused 437
Receiving objects: 100% (489/489), 353.18 KiB | 2.25 MiB/s, done.
Resolving deltas: 100% (246/246), done.
```

Edit the file to suit your own needs. In this example, we will modify the `sysmon.conf` file to exclude Splunk forwarder events (more on configuring Splunk later) by adding the following lines to the `<ProcessCreate onmatch="exclude">` section:

```
<ProcessCreate onmatch="exclude">
        <!--SECTION: Exclude Splunk Forwarder Events-->
        <Image condition="begin with">C:\Program Files\Splunk\bin\</Image>
        <ParentImage condition="is">C:\Program Files\Splunk\bin\splunkd.exe</ParentImage>
        <ParentImage condition="is">C:\Program Files\Splunk\bin\splunk.exe</ParentImage>
        <Image condition="begin with">C:\Program Files\SplunkUniversalForwarder\bin\</Image>
        <Image condition="begin with">C:\Program Files\SplunkUniversalForwarder\bin\</Image>
        <ParentImage condition="is">C:\Program Files\SplunkUniversalForwarder\bin\splunkd.exe</ParentImage>
        <ParentImage condition="is">C:\Program Files\SplunkUniversalForwarder\bin\splunk.exe</ParentImage>
```

Commit the changes in `git`:

```text
git commit -a
```

Output:

```
jemurray@phalanges:~/Documents/security-configs/sysmon-config $ git commit -a
[master 62c7ffd] added splunk exclude
 1 file changed, 12 insertions(+), 4 deletions(-)
```

Push the changes back to the repository:

```text
git push
```

Output:

```
jemurray@phalanges:~/Documents/security-configs/sysmon-config $ git push
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 12 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 522 bytes | 522.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (2/2), completed with 2 local objects.
To github.com:0xJasonMurray/sysmon-config.git
   1836897..62c7ffd  master -> master
```

By storing the `sysmon` config in a public GitHub repository, we can quickly retrieve the latest version of the file and apply it to our Windows 10 machine with the following powershell command:

```text
Invoke-WebRequest -Uri https://raw.githubusercontent.com/0xJasonMurray/sysmon-config/master/sysmonconfig-export.xml -Outfile sysmon-config.xml
```

Now that we have the configuration file staged and ready to go, it's time to download the latest version of `sysmon` using this powershell command:

```text
Invoke-WebRequest -Uri https://download.sysinternals.com/files/Sysmon.zip -Outfile Sysmon.zip
```

Unzip the `Sysmon.zip` file using this powershell command:

```text
Expand-Archive -Path Sysmon.zip
````

Start `sysmon` with our configuration file using this powershell command:

```text
 .\Sysmon\Sysmon.exe -accepteula -i .\sysmon-config.xml
 ```

 Output:

 ```
PS C:\Users\jemurray> .\Sysmon\Sysmon.exe -accepteula -i .\sysmon-config.xml

System Monitor v13.30 - System activity monitor
By Mark Russinovich and Thomas Garnier
Copyright (C) 2014-2021 Microsoft Corporation
Using libxml2. libxml2 is Copyright (C) 1998-2012 Daniel Veillard. All Rights Reserved.
Sysinternals - www.sysinternals.com

Loading configuration file with schema version 4.50
Sysmon schema version: 4.81
Configuration file validated.
Sysmon installed.
SysmonDrv installed.
Starting SysmonDrv.
SysmonDrv started.
Starting Sysmon..
Sysmon started.
```

Validate the process is running and the configuration is loaded:

```
PS C:\Users\jemurray> sysmon -c


System Monitor v13.30 - System activity monitor
By Mark Russinovich and Thomas Garnier
Copyright (C) 2014-2021 Microsoft Corporation
Using libxml2. libxml2 is Copyright (C) 1998-2012 Daniel Veillard. All Rights Reserved.
Sysinternals - www.sysinternals.com

Current configuration:
 - Service name:                  Sysmon
 - Driver name:                   SysmonDrv
 - Config file:                   .\sysmon-config.xml
 - Config hash:                   SHA256=185A746EE9ED4E5B088F241EA7724C9126015CE5ABC1B2FED36792A14D85D282

 - HashingAlgorithms:             MD5,SHA256,IMPHASH
 - Network connection:            enabled
 - Archive Directory:             -
 - Image loading:                 disabled
 - CRL checking:                  enabled
 - DNS lookup:                    enabled
```

To make this process easier to deploy on multiple machines, create the powershell script `installsysmon.ps1` with the following lines:

```
Invoke-WebRequest -Uri https://raw.githubusercontent.com/0xJasonMurray/sysmon-config/master/sysmonconfig-export.xml -Outfile sysmon-config.xml
Invoke-WebRequest -Uri https://download.sysinternals.com/files/Sysmon.zip -Outfile Sysmon.zip
Expand-Archive -Path Sysmon.zip
.\Sysmon\Sysmon.exe -accepteula -i .\sysmon-config.xml
```

Running `installsysmon.ps1` looks like this:

```
PS C:\Users\jemurray> .\installsysmon.ps1

System Monitor v13.30 - System activity monitor
By Mark Russinovich and Thomas Garnier
Copyright (C) 2014-2021 Microsoft Corporation
Using libxml2. libxml2 is Copyright (C) 1998-2012 Daniel Veillard. All Rights Reserved.
Sysinternals - www.sysinternals.com

Loading configuration file with schema version 4.50
Sysmon schema version: 4.81
Configuration file validated.
Sysmon installed.
SysmonDrv installed.
Starting SysmonDrv.
SysmonDrv started.
Starting Sysmon..
Sysmon started.
```


In a later post, we will look at how to send `sysmon` logs into Splunk.  For now, here is an example of `sysmon` logging all `powershell` executed commands:

[![Image of sysmon logging powershell commands into Splunk](/images/2021-10-29-17-01-43.png)](/images/2021-10-29-17-01-43.png)