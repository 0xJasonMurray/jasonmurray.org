---
title: "Setup Opencanary Honeypot with Pushover Notifications"
date: 2022-10-23T16:59:08-05:00
toc: false
images:
categories:
  - tech
tags: 
  - opencanary
  - pushover
  - honeypot
  - security
description: Setting up the free Opencanary honeypot with Pushover text message notifications.
---

## Overview

Detecting malicious activity on your organizations network is critical to find and track down compromised or rogue devices. Dedicated [Opencanary](https://github.com/thinkst/opencanary) honeypot servers scattered throughout the network can quickly detect and alert when any device interrogates it's services.

## Assumptions and Environment

* Dedicated SFF computers (NUC, Raspberry PI's, etc.)
* Base operating system: Ubuntu 22.04LTS server
* Working [Pushover](https://pushover.net/) account (I use the paid version)

## Technical Details

Instead of using Docker or Python virtual environments, I've dedicated an entire machine to this honeypot. The goal is run these devices on SFF computers, plant them all around the network, and reduce complexity.  

* Install operating system prerequisites:
  ```text
  sudo apt-get install python3-dev python3-pip python3-virtualenv python3-venv python3-scapy libssl-dev libpcap-dev samba
  ```
* Install Opencanary and it's prerequisites:
  ```text
  sudo pip install opencanary scapy pcapy opencanary-correlator
  ```
* Create a base config:
  ```text
  sudo opencanaryd --copyconfig
  ```
* Edit the config. Enable appropriate services. In my example, I disabled any services enabled by default, then enabled `portscans` and added the Pushover webhooks:
  ```text
  sudo vi /etc/opencanaryd/opencanary.conf
  ```
* Enable port scans:
  ```text
  "portscan.enabled": true,
  "portscan.ignore_localhost": true,
  ```
* Setup Pushover webhook in the `"handlers": {` section:
  ```text
  "Webhook": {
    "class": "opencanary.logger.WebhookHandler",
    "url": "https://api.pushover.net/1/messages.json",
    "method": "POST",
    "data": {
    "message": "%(message)s",
    "retry": "30",
    "expire": "60",
    "priority": "2",
    "token": "MY-TOKEY",
    "user": "MY-USER-ID"
      },
    "status_code": 200,
    "ignore": ["\"dst_port\": -1"]
  }
  ```
* Create the Opencanary startup script
  ```text
  vi /etc/systemd/system/opencanary.service
  ```
  With the following text:
  ```text
  [Unit]
  Description=OpenCanary honeypot
  After=network.target

  [Service]
  User=root
  Restart=always
  WorkingDirectory=/var/tmp
  ExecStart=/usr/local/bin/opencanaryd --start
  ExecStop=/usr/local/bin/opencanaryd --stop
  ExecRestart=/usr/local/bin/opencanaryd --restart
  Type=forking

  [Install]
  WantedBy=multi-user.target
  Alias=opencanary.service
  ```
* Start the Opencanary service:
  ```text
  sudo systemctl start opencanary
  ```
* Review the log file:
  ```text
  tail -f /var/tmp/opencanary.log
  ```

Note: Pushover is just one way to receive quick notifications if the system is interrogated. I've also installed the [Splunk Universal Forwarder](https://www.splunk.com/en_us/download/universal-forwarder.html) on these systems and log all events into Splunk. Then, by using Splunk alerts, I am notified of any events based on aggregated alerts and/or configured thresholds.  