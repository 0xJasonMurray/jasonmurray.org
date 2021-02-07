---
title: "Google Wifi diagnostic menus"
date: 2020-07-26T13:09:02-05:00
toc: false
images:
categories:
  - tech
tags: 
  - googlewifi
---


Diagnostic pages for the Google Wifi home router:


- http://onhub.here/ - Basic system status (online / offline).  Open the Google Wifi app to access more information.
- http://on.here - Check all of the devices that are available on the guest network. For example, guests who are using the Wifi network, can control Philips Hue lights directly with On.Here without needing a separate app.
- http://onhub.here/api/v1/diagnostic-report - Generate and download a detailed diagnostic report.
- http://onhub.here/api/v1/welcome-mat - Information about the guest network.
- http://onhub.here/api/v1/status - Software version, systems hardware ID, WAN settings, etc.
- http://onhub.here/api/v1/connected-devices - Hue bridge information.
- http://onhub.here/api/v1/get-attestation-information - No longer returns anything.
- http://onhub.here/api/v1/get-endorsement-information - No longer returns anything.
- http://onhub.here/api/v1/get-group-configuration - Google Wifi group id.
- http://onhub.here/api/v1/get-shmac?ip=insertClientIP - Device ID for a given IP.
- http://onhub.here/api/v1/wan-configuration - WAN configuration.
- http://onhub.here/api/v1/developer-configuration - Update URL and channel.


These domains can be used to access the local pages:

- http://testwifi.here
- http://onhub.here