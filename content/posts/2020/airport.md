---
title: "Using the macOS command airport to perform wireless signal strength scans"
date: 2020-06-22T16:25:29-05:00
toc: false
images:
categories:
  - tech
tags: 
  - airport
  - macOS
  - wireless
---

There is a hidden command in macOS, `airport`, which is used to view wireless signal strength from the command line.  By wrapping the `airport` command in a simple shell script, we can search for and sort wireless networks by their signal strength.  

I have used this command to perform site surveys and validation of signal strength after wireless hardware deployment.   

The `airport` command is not in the default search $PATH: `/System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport`.  


Symlink the command into `/usr/local/bin/` to make it easier to remember:

```
ln -s /System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport /usr/local/bin/airport
```

The following script performs the signal strength testing:

```
#!/bin/bash

SSID=$1

while [ 1 ] ; do
    date
    airport -s | egrep "$SSID" | sort -k 3 -n -r
    sleep 1
done
```

This script takes a wireless network SSID as the input.  It will then scan for all access points broadcasting the SSID and sort them by signal strength:

```
jemurray@mbp-2019:~/Documents/scripts/shell $ ./airport-scan.sh msecure
Mon Jun 22 16:24:50 CDT 2020
                         msecure 58:cb:52:d8:fc:28 -37  11      Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d9:07:5f -47  6       Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d8:fc:24 -48  149     Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d9:07:5b -56  149     Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d8:fc:6a -57  1       Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d8:fc:66 -62  149     Y  us WPA2(PSK/AES/AES)
Mon Jun 22 16:24:55 CDT 2020
                         msecure 58:cb:52:d8:fc:28 -37  11      Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d8:fc:24 -46  149     Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d9:07:5f -47  6       Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d9:07:5b -56  149     Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d8:fc:6a -58  1       Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d8:fc:66 -61  149     Y  us WPA2(PSK/AES/AES)
Mon Jun 22 16:25:00 CDT 2020
                         msecure 58:cb:52:d8:fc:28 -41  11      Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d8:fc:24 -46  149     Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d9:07:5f -47  6       Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d9:07:5b -56  149     Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d8:fc:6a -58  1       Y  us WPA2(PSK/AES/AES)
                         msecure 58:cb:52:d8:fc:66 -62  149     Y  us WPA2(PSK/AES/AES)
```

I will run this command and slowly walk around a room or building marking off the signal strength for surveys or adjust wireless hardware as necessary to fix signal strength issues.  It is a poor man’s wireless survey tool.

