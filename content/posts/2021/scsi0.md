---
title: "Fixing vmware boot error: Unsupported or invalid disk type 2 for 'scsi0:0'"
date: 2021-09-15T20:46:38-05:00
toc: false
images:
categories:
  - tech
tags: 
  - vmware
  - metasploitable
  - vmware
description: How to fix the vmware error - Failed to power on virtual machine Metasploitable2-Linux. Unsupported or invalid disk type 2 for 'scsi0:0'. Ensure that the disk has been imported. Click here for more details.
---

After downloading an old version of [Metasploitable](https://information.rapid7.com/download-metasploitable-2017.html), the following error appears when trying to boot:

```
Failed to power on virtual machine Metasploitable2-Linux. Unsupported or invalid disk type 2 for 'scsi0:0'. Ensure that the disk has been imported. Click here for more details. 
```

To fix the problem, `ssh` into the `vmware` host and run the command:

```text
vmkfstools -i Metasploitable.vmdk MetasploitableFixed.vmdk
```

Full output:

```
[root@vm02:/vmfs/volumes/5e909c57-02b960e8-e12a-d4ae52a130ac/metasploitable] vmkfstools -i Metasploitable.vmdk MetasploitableFixed.vmdk
Destination disk format: VMFS zeroedthick
Cloning disk 'Metasploitable.vmdk'...
Clone: 100% done.
```



Delete the broken hard disk:

[![Image of ](/images/2021-09-15-21-01-38.png)](/images/2021-09-15-21-01-38.png)

Add a new hard disk:

[![Image of ](/images/2021-09-15-21-02-28.png)](/images/2021-09-15-21-02-28.png)

Select the fixed disk image from the command above:

[![Image of ](/images/2021-09-15-21-03-16.png)](/images/2021-09-15-21-03-16.png)


Boot the vm.

