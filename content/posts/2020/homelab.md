---
title: "My home lab setup"
date: 2020-05-31T10:55:26-05:00
toc: false
images:
categories:
  - tech
tags: 
  - homelab
  - esxi
  - electronics
  - gns3
  - virl
  - servers
  - cisco
  - juniper
  - arduino
---

# Summary

As an infrastructure architect, the lab environment plays a significant role to validate design ideas, confirm configuration changes, and build new skills.

My home lab has evolved over the past 20 years, moving from a complete rack of hardware, down to a single server, back to multiple servers, then completely outsourced to the cloud. In recent years the physical servers have returned to run compute hungry workloads like GNS3 and VIRL, as well as ESXi appliances that are easier to host locally than from remote.  

# Physical equipment

The lab begins with a 19" rack in the basement. In the rack from top to bottom is the cable modem, Juniper and Cisco routers, switches, and firewalls, Dell and HP servers running ESXi, power strips and a UPS.

This is a working lab, the cables are not pretty, cable management is non-existant, and the rack is not back lit with neon lights.  It is changing, new parts are added, old parts are removed, new equipment is tested, cables are rerouted constantly.  I prefer flexibility and rapid development over looks.

![rack](/images/homerack.JPG)

An undersized UPS supplies the power.  The UPS was originally purchased, when the lab was outsourced to the cloud and the only devices needed to remain up during a power outage were the cable modem, wireless access points, and router. While it is working for a single server, running both is not possible.   Underneath the Dell server are two APC SmartUPS 1000’s, but the batteries are dead:

![ups](/images/homeups.JPG)

Example power usage from the Dell server:

![dell power usage](/images/dellpowerusage.png)


# Virtual Servers

The 1U HP server is an old ESXi server currently being migrated to a new Dell with significantly more capacity. This system is running all the light workloads:

![vmware](/images/vm01.png)

The 2U Dell is the workhorse of the home lab, all VM servers will be eventually [migrated](/posts/2020/ovftool/) here. Systems like VIRL and GNS3 use a lot of resources.

![vmware](/images/vm02.png)


# Remote access

Linux VMs running on the ESXi server is where remote console and development work takes place.  `tmux` allows me to start and stop work while easily picking up where I left off.  


![terminal](/images/homelabterminal.png)

OpenVPN running on the home-server and Dynamic DNS provides remote access into the lab:

![tunnel blick](/images/homelabtunnelb.png)

Configuring OpenVPN is made simple by [openvpn-install](https://github.com/angristan/openvpn-install):

```
jemurray@home-server:~$ history | grep open
  225  curl -O https://raw.githubusercontent.com/angristan/openvpn-install/master/openvpn-install.sh
  226  chmod 755 ./openvpn-install.sh
  228  sudo ./openvpn-install.sh
```

# Network virtulization and design

GNS3 is used to model network designs.  The server is hosted on the ESXi farm so that there is enough CPU and memory to design large topologies. I legally get images from a Cisco VIRL license and a Juniper support contract.   

![gns3](/images/gns3.png)

While I prefer to use GNS3 for most network modeling projects, I pay for [VIRL](http://virl.cisco.com/) to get legal Cisco images which work in both VIRL and GNS3.

![virl](/images/virl.png)

# Monitoring

Smokeping monitors connectivity internally and externally. In the past there were problems with my local ISP, tools like Smokeping make it easier to make a case when dealing with the support personal:

![smokeping](/images/smokeping.png)

# SaaS Services

Digital Ocean hosts my remote Linux servers. Even though I could host these servers at home using [dynamic DNS](/posts/2020/ansibledynamicdns/), it is nice to have a well-connected system on the public Internet:

![digital ocean](/images/digitalocean.png)

Route53 hosts DNS for my public and lab subdomains. I moved DNS to a hosted solution so that changes in the lab do not affect my personal “production” services:

![route53](/images/route53.png)

Even a lab environment should have 2FA enabled. In fact, I have 2FA enabled for every public service with this feature enabled. LastPass authenticator generates the tokens:

![2fa](/images/2falab.png)

# Home LAN

[Google WIFI](https://store.google.com/us/product/google_wifi_first_gen) supplies connectivity to the lab and the house. This is one area that is constantly evolving from a cheap big box router, to pfSense, vanilla Linux, Ubiquity edge, Cisco ASA, and Juniper SRX, the lab has seen them all. Why Google WIFI?  Because my family is tired of constant changes and complexity. Having a device that someone other than me can troubleshoot is important to my family. Having your wife or kids serial console to a Juniper SRX because YouTube TV is not working is a showstopper:

![google wifi top](/images/googlewifitop.jpg)

![google wifi bottom](/images/googlewifibottom.jpg)


# Electronics


In additional to the network and server hardware, I enjoy experimenting with electronics. Over the years I have collected various sensors, motors, servos, Arduino, Raspberry PI’s, and soldering equipment. I started playing around in this area, while trying to find inexpensive network connected relays for controlling power and sensors to monitor conditions in the house.

![electronics](/images/electronicsdesk.jpg)

# Old Parts

Finally, no home lab would be complete without a bone yard of old parts. Over time, the pile has dwindled, the shelves cleaned, and boxes emptied. A few years ago an entire pickup truck load of old equipment was shipped off to an electronic recycling center. Getting rid of this old hardware is one more step to keeping me off the horders TV show:

![bone yard](/images/boneyard.jpg)
