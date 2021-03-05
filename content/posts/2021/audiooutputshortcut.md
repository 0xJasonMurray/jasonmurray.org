---
title: "Using iOS Shortcuts to select between audio destinations"
date: 2021-03-05T09:57:27-06:00
toc: false
images:
categories:
  - tech
tags: 
  - ios
  - shortcuts
description: How to create an iOS Shortcut workflow to quickly switch between audio destinations.
---

## Overview

At the end of the day, I take off my bluetooth headset and put it on the charger in the upstairs office. I usually forget to power it off, therefor it stays connected to my phone. Typically not a problem unless I want to listen to an audio book or watch a video. Not all iOS apps have a button to quickly switch between audio destinations. In these instances, I have to open the `Settings -> Bluetooth -> Device -> Disconnect` menu. It would be much easier if there was a button or widget to quickly switch between audio outputs.

I am not alone, quickly switching audio outputs is a common question with users on iOS devices. While there is no "native" button to accomplish this task, using the [iOS Shortcuts app](https://support.apple.com/guide/shortcuts/welcome/ios), we can create a button or widget to perform the tasks needed to switch audio devices.

The next few screen shots detail how I created a workflow in `shortcuts` to switch audio outputs with the touch of a button on the widgets menu:

## Details

Open the `Shortcuts` app and select the `+` in the upper right corner:

[![Image of iOS Shortcut Menu](/images/IMG_1639.jpeg)](/images/IMG_1639.jpeg)

Select `Add Action`:

[![Image of iOS Shortcut Menu](/images/IMG_1640.jpeg)](/images/IMG_1640.jpeg)

Select `Scripting`:

[![Image of iOS Shortcut Menu](/images/IMG_1641.jpeg)](/images/IMG_1641.jpeg)

Select `Set Playback Destination`:

[![Image of iOS Shortcut Menu](/images/IMG_1642.jpeg)](/images/IMG_1642.jpeg)

Set the destination to `iPhone`.  In a later step, we will select other devices:

[![Image of iOS Shortcut Menu](/images/IMG_1643.jpeg)](/images/IMG_1643.jpeg)

Name the shortcut to `Set Playback to iPhone`:

[![Image of iOS Shortcut Menu](/images/IMG_1644.jpeg)](/images/IMG_1644.jpeg)

Repeat the steps above to add other output destinations.  In this example, a `Jabra Elite 65e` headset is added:

[![Image of iOS Shortcut Menu](/images/IMG_1645.jpeg)](/images/IMG_1645.jpeg)

Confirm the output device and click next:

[![Image of iOS Shortcut Menu](/images/IMG_1646.jpeg)](/images/IMG_1646.jpeg)

Set the name to `Set Playback to Headset`:

[![Image of iOS Shortcut Menu](/images/IMG_1647.jpeg)](/images/IMG_1647.jpeg)

For quick access, add the shortcuts to the `widgets` menu:

[![Image of iOS Shortcut Menu](/images/IMG_1648.jpeg)](/images/IMG_1648.jpeg)

Now I can either say, "Hey Siri, set playback to headset", "Hey Siri, set playback to iPhone", or click a button from the widgets screen instead of the more complicated `settings` menu.