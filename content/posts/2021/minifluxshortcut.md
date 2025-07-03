---
title: "Creating an iOS shortcut to add RSS news feeds to Miniflux"
date: 2021-03-13T15:28:57-06:00
toc: false
images:
categories:
  - tech
tags: 
  - miniflux
  - ios
  - shortcuts
  - api
  - restful
description: Creating and installing an iOS shortcut to add RSS news feeds to Miniflux.
---

## Overview

[Miniflux](https://miniflux.app/) is a minimalist RSS feed reader.  It does not have an iOS application to facilitate adding new feeds through the share button. To compensate, I created an iOS shortcut which uses the Miniflux API to discover the RSS URL and add it to the feeds list.

## Installation Instructions

- Create and copy an API key here: https://reader.miniflux.app/keys
- Determine the category ID for all new feeds here: https://reader.miniflux.app/v1/categories
- Save the API key and Category ID. The import process will ask for this information during installation.
- Download the shortcut here: https://www.icloud.com/shortcuts/dddc998202db45b7931c254781df3beb

Select `Get Shortcut`:

[![Image of add to miniflux](/images/IMG_1702.PNG)](/images/IMG_1702.PNG)

Select `Add Untrusted Shortcut`:

[![Image of add untrusted](/images/IMG_1703.PNG)](/images/IMG_1703.PNG)

Fill in the `API Key` and `Category ID`:

[![Image of asking question](/images/IMG_1704.PNG)](/images/IMG_1704.PNG)

## Adding a Feed Example

After installation, attempt to add an RSS feed to Miniflux.

From `Safari` click `Share -> Add to Miniflux`:

[![Image of ](/images/IMG_1699.PNG)](/images/IMG_1699.PNG)

Notification from discovery:

[![Image of ](/images/IMG_1700.PNG)](/images/IMG_1700.PNG)

Notification after adding feed:

[![Image of ](/images/IMG_1701.PNG)](/images/IMG_1701.PNG)

## Creation Details

Details for creating the `Add to Miniflux` shortcut:

[![Image of Shortcut menu ](/images/IMG_0023.PNG)](/images/IMG_0023.PNG)

The shortcuts accepts a URL as input from the `share` button and stores the value in the `SHAREURL` variable:

[![Image of get URL from share](/images/2021-03-13-15-37-00.png)](/images/2021-03-13-15-37-00.png)

To facilitate sharing this URL without hardcoding my API keys, we set two additional variables.

The Miniflux API key is stored in `APIKEY` and the default category is stored in `CATEGORYID`. When a user imports a shortcut for the first time, `import questions` prompt for the API key and default category:

[![Image of setting variables](/images/2021-03-13-15-37-35.png)](/images/2021-03-13-15-37-35.png)

When a URL is `posted` to the `https://reader.miniflux.app/v1/discover` API endpoint, Miniflux will attempt to discover the RSS feed URL. The `url` is returned in a `json` structure.  The `scripting` module parses and sets a `dictionary value` variable with the contents of the URL:

[![Image of discover RSS feed ](/images/2021-03-13-15-38-19.png)](/images/2021-03-13-15-38-19.png)

Notifications display the discovery results:

[![Image of rss discovery notification](/images/2021-03-13-15-38-47.png)](/images/2021-03-13-15-38-47.png)

After discovery, the URL it is `posted` to the API endpoint `https://reader.miniflux.app/v1/discover`.  Miniflux will attempt to add the feed in the default category:

[![Image of add feed](/images/2021-03-13-15-39-55.png)](/images/2021-03-13-15-39-55.png)

Notifications display the feed add results:

[![Image of feed add notification](/images/2021-03-13-15-40-28.png)](/images/2021-03-13-15-40-28.png)


