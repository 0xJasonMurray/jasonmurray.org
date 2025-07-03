---
title: "Even Apple listens"
date: 2020-08-11T13:39:41-05:00
toc: false
images:
categories:
  - tech
tags: 
  - apple
---

A few days ago I wrote a [post](/posts/2020/appleprivatewifi/) about a change Apple was making in iOS14.  If implemented, it would cause the MAC address of network-connected devices to change every 24 hours.  This has the potential to wreak havoc on many complicated and expensive enterprise systems in use on large campus networks like ours.

In response, the university community came together [in email](https://listserv.educause.edu/scripts/wa.exe?A1=ind2007&L=WIRELESS-LAN#10) to discuss the impact of this change.  Everyone was encouraged to reach out to Apple with their specific issues and let them know the impact this would bring about.

I composed this email to Apple:

> On behalf of Washington University in St. Louis, our organization has some questions and concerns about Apples implementation of private Wi-Fi addressing in iOS14 and watchOS7.  There is significant speculation surrounding the rollout of this new feature.   Apple has not released enough concrete information for large network providers like us to make informed decisions on how to accommodate the potential changes.
>
> First, could you address these questions:
>
> - Will the private Wi-Fi addressing feature be enabled by default?
> - If so, when will it be deployed to the general user population?
> - Will this feature make its way into macOS and would it be enabled by default?
>
> To give Apple perspective a change like this would have, here is a brief overview of the Washington University network and a few of the critical systems that rely on a consistent MAC address.
>
> Our campus network averages 75,000+ simultaneous devices online every day.   To provide an excellent user experience, we offer multiple wireless connection options.   This includes an open network for guests, MAC authentication via captive portal for students and gaming devices, and 802.1x for more secure business needs.
>
> A change in the default behavior of MAC address generation and consistency, has consequences that extend into several systems the university owns or operates.  While we acknowledge the privacy concerns Apple is trying to address, many backend systems rely on a devices MAC address to remain constant and unique.  Changing this behavior is difficult for a large university network like ours to handle with such short notice.
>
> Here are a few examples of critical and complex systems that will be impacted:
>
> **Captive Portal**: This system manages 100k+ student, faculty, and staff devices.  It relies on the MAC address as the unique identifier to determine who is authorized to use the network.   This system is designed to register devices once every 4 years.   If the devices have to register once a day, we will need to replace this service with a new system capable of handling the load.  
>
> **Mobile Device Manager**: Devices that connect to our higher privilege networks require registration in a mobile device management system.  There are currently 20k+ devices under active management and all key off the MAC address to function properly.  
>
> **Device Troubleshooting Tools**: Our support desk uses various tools to help troubleshoot user problems.   There are approximately 300k+ devices tracked in this system.  The MAC address is the primary key for finding and helping our users.
>
> **IPAM / DHCP**: Multiple /16 networks are associated with the wireless controllers.  We are concerned the added load on our DHCP servers will require us to replace the service with new hardware sooner than we expected.   
>
> I look forward to working with Apple to address these concerns and finding ways we can implement stronger privacy features without causing negative impact to our campus users.   


To my surprise, I received a personal response that addressed all the questions and concerns we raised:

> Thanks for your thoughtfully written email as I can appreciate your concern(s). First off, I want answer your direct questions to the best of my current ability, so please see those answers below:
>
> * Will the private Wi-Fi addressing feature be enabled by default?
>   * As of today, as you’ve noted, we only have a limited amount of information about this feature in iOS/iPadOS 14 and watchOS 7, but I can tell you that it will be enabled by default. You can reference the support doc, Use private Wi-Fi addresses in iOS 14, iPadOS 14, and watchOS 7, to see all of the available info we currently have about how it works, and how to disable it. 
> * If so, when will it be deployed to the general user population?
>   * As of now, you can assume this feature will be included in the shipping release of iOS/iPadOS 14 and watchOS 7, but we have not published a release date for those OS versions outside of ’this Fall’. 
> * Will this feature make its way into macOS and would it be enabled by default?
>   * We have talked about whether or not that feature will be adopted by macOS, and even though I wouldn’t be surprised to see it added, I have no information one way or another. 
> 
> As a general statement, Apple believe that using MAC addresses to authenticate wireless networks doesn’t provide the level of security that other forms of network authentication, like 802.1X, provides, and is not a great user experience from a privacy perspective. We have published a support doc that includes our recommendations called Recommended settings for Wi-Fi routers and access points. 


A week later I saw a new [post](https://listserv.educause.edu/scripts/wa.exe?A2=ind2008&L=WIRELESS-LAN&P=33663) on the Educause forums that indicated Apple may have rolled back the 24 hour randomization feature.  

To be certain, I sent another email to Apple and received this personal response within minutes:


> You beat me to it. Yes, we have been listening to all of the concerns expressed by customers, such as yourselves, about this change, and have adjusted the way things work. This would be a textbook case of why AppleSeed for IT testing is important during the summer months before our fall releases - so we can make adjustments to the OSs where needed prior to the initial release. 


The lesson to be learned.  No matter how big the pond, a bunch of little fish can have their voice heard.
