---
title: "Simulating production networks with Cisco Modeling Lab: Install Guide"
date: 2021-02-13T11:41:28-06:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - cml
  - virl
  - simulation
  - lab
description: Guide to the download, installation, configuration, and initial lab setup of a Cisco Modeling Lab server on vmware ESXi server.
---

## Overview

[Cisco Modeling Lab](https://www.cisco.com/c/en/us/products/cloud-systems-management/modeling-labs/index.html) is a commercial network virtualization environment. All routers, switches, and security devices are licensed and included within the CML system.  CML differs from other offerings such as [GNS3](https://www.gns3.com/) or [Eve-NG](https://www.eve-ng.net/) by including legal Cisco software images and licenses as part of the purchased bundle.  The software costs approximately $200 per year for the [personal edition](https://learningnetworkstore.cisco.com/cisco-modeling-labs-personal/cisco-cml-personal) which allows up to 20 simultaneous devices.  

While the minimum system requirements are 4 CPUs, 8Gb RAM, and 16Gb HD; this won't be enough to run 20 routers at the same time.  Expect to spend a few thousand dollars on hardware capable of simulating a real production network.  This is not software that will run well on a typical consumer laptop.  

## Details  

Start by downloading the CML software.  As noted before, it's not free.  Cisco is charging $200 per year for the personal edition:

```
https://learningnetworkstore.cisco.com/myaccount
```

Click `Download`:

![Purchasee and license screen](/images/2021-02-12-16-07-48.png)

For new installations, download the latest 2.1.2 version: 

![Download screen](/images/2021-02-12-16-01-40.png)

In addition to the CML software, the `Personal Reference Platform` ISO image is required during the installation.  This software is not found in the 2.1.2 download screen.  At the time of this writing, `PRP` is in the 2.0 download section:

![2.0 download section of the personal reference platform download](/images/2021-02-12-16-01-59.png)

All downloads require accepting the `End User Software License Agreement`:

![agree to EULA](/images/2021-02-12-16-03-46.png)

When the installation begins, the `Personal Reference Platform` ISO image must be mounted during boot up.  Prepare for the upcoming step, by uploading the ISO to the the vmware `datastore` repository so it can be mounted as a `Datastore ISO file` (instructions below):

![uploading refplat](/images/2021-02-12-16-16-01.png)

Validate the `PRF` image uploaded successfully:

![datastore browswer with refplat uploaded](/images/2021-02-12-16-48-08.png)

Start the installation by creating a new virtual machine from `ova`:

![install from ova](/images/2021-02-12-19-29-08.png)

Name the image and select the OVA:

![name and add ova](/images/2021-02-12-16-50-02.png)

Select the `datastore` location for the OVA installation:

![select datastore](/images/2021-02-12-16-50-28.png)

Select a network that provides the CML server access to the public Internet.  Uncheck the `Power on Automatically` option (incorrect in the image).  Before the system boots up for the first time, a few settings must be changed:

![Select management network and provisioning type](/images/2021-02-12-16-50-54.png)

Watch the system build for a few minutes:

![building VM](/images/2021-02-12-16-52-01.png)

Before CML boots for the first time, change the `CPU`, `Memory`, and `Disk` settings.  Minimum requirements are `4`, `8G`, and `16G` respectively.  However, modeling devices of any scale will require significantly more resources.  Add as much as possible.

The `Personal Reference Platform` ISO DVD must be mounted and configured to `Connect` at startup.  If the DVD is not connected, the Cisco router and switch images will not install:

![adjust settings before powering on](/images/2021-02-12-16-53-30.png)

The following ESXi option `Expose hardware assisted virtualization to guest OS` must be enabled:

![validate virtualization is exposed to the guest](/images/2021-02-12-16-56-12.png)

Boot the system:

![CML booting for the first time](/images/2021-02-12-14-41-56.png)

Accept the EULA:

![access the EULA](/images/2021-02-12-14-42-20.png)

Information about account creation and networking:

![Information about account creation and networking](/images/2021-02-12-14-42-40.png)

Information about screen navigation and shortcuts:

![Information about screen navigation and shortcuts](/images/2021-02-12-14-43-07.png)

If the DVD is not mounted during installation, the following error will display.  The installation will not have the Cisco router and switch images if this error is not resolved.  Edit settings and `Connect` the DVD image:

![missing disk](/images/2021-02-12-14-43-38.png)

Create `sysadmin` account:

![create sysadmin account](/images/2021-02-12-14-51-03.png)

Create `admin` account:

![create admin acccount](/images/2021-02-12-14-51-29.png)

Once installation is complete, the console displays the IP address necessary to access the web interface:

![Console booted up with IP address](/images/2021-02-12-18-39-02.png)

Browse to the IP address displayed in the console window and login with the `admin` account:

![new installation login screen](/images/2021-02-12-14-52-34.png)

A new installation has no license:

![no license](/images/2021-02-12-14-53-09.png)

Open the license menu `Tools -> Licensing`:

![](/images/2021-02-12-14-56-50.png)

The license key is found on the Cisco website `https://learningnetworkstore.cisco.com/myaccount`.  Select the `2.0 Licensee` and copy the string:

![Purchase and license screen](/images/2021-02-12-16-07-48.png)

Click the `Register` button:

![Click the register button](/images/2021-02-12-14-57-24.png)

Paste in the license code:

![Paste in the license code](/images/2021-02-12-14-58-15.png)

Status will change to `Authorized` once the key is validated with the Cisco licensing server:

![license success](/images/2021-02-12-14-58-53.png)

Start building a lab.   Try a few samples in `Tools -> Sample Labs`:

![Example lab booting up](/images/2021-02-12-18-39-36.png)

Examples of the routers, switches, and servers available for provisioning:

![example devices](/images/2021-02-12-19-59-51.png)