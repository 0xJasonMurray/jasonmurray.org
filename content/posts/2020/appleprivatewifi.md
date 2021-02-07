---
title: "iOS 14 will randomize the MAC address every 24 hours"
date: 2020-08-07T16:01:49-05:00
toc: false
images:
categories:
  - tech
tags: 
  - apple
  - ios14
  - privatewifi
---

Private Wi-Fi address is a new feature Apple is enabling by default starting with iOS14 (iPhone and iPad) and watchOS7 (Apple Watch).  This feature works by randomizing the MAC address of the device before it connects to the wireless network and every 24 hours thereafter.

A MAC address is a unique address assigned to every device that connects to a wireless or wired network.  The address looks like this: f0:18:98:9b:b5:b2.  The first half (f0:18:98) is known as the organizational unique identifier (OUI).  This part is assigned to a manufacture such as Apple, Dell, or Intel.  The second half (9b:b5:b2) is assigned by the vendor.  The combination of these two parts makes each MAC address unique to each individual device.  No two MAC addresses overlap.  

Apple, and other privacy experts, believe this unique address can be used for tracking and profiling purposes.  Did you believe the local grocery store was giving away free WiFi to be a good neighbor?  A unique MAC address allows these organizations to track you over time.  They know when you arrive, how you move through the store, and how long you stop in an isle.  Over time a profile of a persons shopping pattern is created and stored.  The result of this information allows stores to enhance layouts or change in-store advertising based on your shopping behaviors.  

Apple is attempting to solve the "long term" tracking problem by randomizing parts of the MAC addresses these organizations use to uniquely identify you.

While Apples change may seem trivial and a step in the right direction for privacy, MAC address standards and uniqueness have been around since ethernet was accepted as a IEEE standard in the early 1980's.  Many backend systems large campus networks use, rely on the MAC address to remain constant and unique.

On average large enterprise systems such as wireless controllers, captive portals, and network access control systems tend to average $100k.  Each of these systems will be affected by this change.  Many captive portal and NAC systems use the MAC address a unique identifier to determine if a device is registered or not.  If the MAC address changes every 24 hours, users will be required to register daily.  Leading to a poor user experience and an increase in trouble tickets.  Unique device report will be impossible to accurately generate.  Troubleshooting user problems will be more difficult when the unique ID is no longer available to track users.   While there are alternative ways around these issues, the limited time Apple has given large enterprises to retool is neither fair to users or the organizations that support them.

Android handled the privacy issue differently.  In 2019, Android 10 enabled random MAC addresses by default.  The key difference is the MAC is random per wireless network and does not change every 24 hours.  Systems that key off a unique MAC address, will still operate fine with this model.

I was curious how many devices are already using a random MAC address.   I work for Washington University in St. Louis, which is a medium sized campus.  On average we have about 75,000 devices online at the same time and see about 100,000 unique devices per day.   Looking over a 24 hour period of DHCP logs there are 740 devices using unique MAC addresses.  Keep in mind, this is curing COVID19.  We have no students on campus and a large number of people working from home.  If you want to know how I figured this out, see [the previous post](/posts/2020/finddynamicmac/).
