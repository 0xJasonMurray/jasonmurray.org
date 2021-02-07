---
title: "Configure WiFi with a QR code"
date: 2020-09-05T13:15:35-05:00
toc: false
images:
categories:
  - tech
tags: 
  - wifi
  - qrcode
---

What if I told you, configuring guests on your home WiFi network is as easy as scanning this QR Code:

![wifi qr code](/images/wifiqrcode.png)

Too many residential routers come with stock wireless names like `MySpectrumWifiNetwork-5G` and a password that takes 5 tries to enter properly on a tiny phone keyboard: `akhajadkjhafd7a7adfhahjadfayadfyuadgf`. Most people will give up after the 2nd try. Leaving your guests to pay for cellular data, instead of sipping the sweet nectar of that One Gig fiber-service you pay so dearly for.

Alas, there is a better way.   By snapping a picture of a QR code, guests will be automatically whisked onto the WiFI network without having to type a single character. This feature is supported on modern versions of iOS and Android.

Creating the QR code is easy.  In fact, there are dozens of [sites](https://qifi.org/) that will help you create one.  As readers of this website, my astute audience tends to be a bit more nerdy and would prefer to know what is going on under the covers.   

The typical home Wifi network has a network name, password, and is secured by WPA.  For example,

* Wireless name: msecure
* Wireless password: QuickBrownFox
* Security: WPA

To generate the QR code for this network, we need to encode the following information:

```
WIFI:T:WPA;S:msecure;P:QuickBrownFox;;
```

Again, there are dozens of sites that generate QR codes, pick your favorite one or paste the information into this API: [https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=WIFI:T:WPA;S:msecure;P:QuickBrownFox;;](https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=WIFI:T:WPA;S:msecure;P:QuickBrownFox;;)

Print the QR code and tape it to the refrigerator:

![wifi qr code](/images/wifiqrcode.png)


What if my network configuration is more complex?  There are options:

|Parameter|Value|Description|
|-----|-----|------|
|S|YourWirelessNetworkName|Required. The wireless network name (SSID).  Can be enclosed in double-quotes if necessary.|
|P|YourWirelessPassword|Optional. The wireless password.  Leave blank for `T:nopass`.|
|T|WPA|Optional. Authentication type.  Options: `WEP`, `WPA`, `WPA2-EAP`, or `nopass` (omit this parameter for `nopass`).|
|H|true|Optional. Set to `true` if the wireless network is hidden.|
|E|TTLS|Enterprise use only, most home networks won't use this option.   EAP Method.  Options: `TTLS`, `PWD`, [etc](https://en.wikipedia.org/wiki/Extensible_Authentication_Protocol).|
|A|anon|Enterprise use only, most home network won't use this option.  Anonymous outer identity.|
|I|MyUserName|Enterprise use only, most home network won't use this option. Inner identity (username).|
|PH2|MSCHAPV2|Enterprise use only, most home network won't use this option.  Phase 2 authentication method.  Options: `MSCHAPV2`, `TLS`, `MD5`, etc.|