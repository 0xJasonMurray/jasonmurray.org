---
title: "Removing Disqus to enhance my readers privacy"
date: 2021-02-08T11:03:17-06:00
toc: false
images:
categories:
  - tech
tags: 
  - privacy
  - disqus
description: When Disqus comments are enabled an additional 24 requests, some to external 3rd parties, are required for each page load.  To address my readers privacy concerns, I removed Disqus commenting.
---

In my haste to add user comments to this website, I did not take into account the privacy of my readers.  While I believe privacy on the Internet is mostly a thing of the past, I don't want my site to be a contributing factor its further erosion.  

I stumbled across this issue while debugging a display problem.  Using the Safari developer tools, we see there are 24 external requests per page load when Disqus commenting is enabled:

[![Image of Safari debug tools showing 24 external pages load while viewing my my static website](/images/2021-02-06-14-23-48.png)](/images/2021-02-06-14-23-48.png)




Probably more concerning to my privacy conscious readers, Disqus uses a number of external third parties.  Multiple requests are logged to Facebook and Google servers for each page load:

```
jemurray@jasons-mbp:~/Downloads $ cat jasonmurray.org.har  | jq -r .log.entries[].request.url | grep -v 'https://jasonmurray.org/'
https://a.disquscdn.com/1611874952/images/noavatar92.png
https://accounts.google.com/o/oauth2/iframerpc?action=checkOrigin&origin=https%3A%2F%2Fdisqus.com&client_id=508198334196-bgmagrg0a2rub674g0shidj8fnd50dji.apps.googleusercontent.com
https://accounts.google.com/o/cspreport
https://accounts.google.com/o/oauth2/iframe#origin=https%3A%2F%2Fdisqus.com&rpcToken=746490973.8318144&clearCache=1
https://accounts.google.com/o/oauth2/iframe#origin=https%3A%2F%2Fdisqus.com&rpcToken=746490973.8318144&clearCache=1
https://apis.google.com/_/scs/apps-static/_/js/k=oz.gapi.en_US.3k1wIje1lec.O/m=auth2/rt=j/sv=1/d=1/ed=1/am=wQE/rs=AGLTcCNT4ir0QEJ6sXXAMZvqjav9vQSaLw/cb=gapi.loaded_0
https://apis.google.com/js/api.js
https://c.disquscdn.com/next/embed/styles/realtime.af77184dec69e96e69aff958ae2bb738.css
https://c.disquscdn.com/next/embed/styles/realtime.af77184dec69e96e69aff958ae2bb738.css
https://c.disquscdn.com/next/embed/lounge.bundle.ec325e7c33ae32f082a2c57fe0c859bd.js
https://c.disquscdn.com/next/embed/assets/font/icons.4cc7a703d2fdfe684151ff8ac24d45f1.woff2
https://c.disquscdn.com/next/embed/styles/lounge.0f8247d0689845c86c5bfcd8efd31a28.css
https://c.disquscdn.com/next/embed/common.bundle.38ea27189bdb723eae3dabf5bc7b8c0b.js
https://c.disquscdn.com/next/embed/lounge.load.d92e3c2dd4530beaef2329d8676faf04.js
https://connect.facebook.net/en_US/sdk.js?hash=b032072813c2a579b33f6035d4ab54f4
https://connect.facebook.net/en_US/sdk.js
https://disqus.com/api/3.0/forums/details?forum=jasonmurray-org&attach=forumFeatures&api_key=E8Uh5l5fHZ6gD8U3KycjAIAk46f68Zw7C6eW8WSjZvCLXebZ7p0r1yrYDrLilk2F
https://disqus.com/next/config.js
https://disqus.com/embed/comments/?base=default&f=jasonmurray-org&t_u=https%3A%2F%2Fjasonmurray.org%2Fposts%2F2021%2Fmonitorwireless%2F&t_d=Monitoring%20layer-1%20wireless%20traffic%20with%20Linux&t_t=Monitoring%20layer-1%20wireless%20traffic%20with%20Linux&s_o=default#version=d92e3c2dd4530beaef2329d8676faf04
https://disqus.com/embed/comments/?base=default&f=jasonmurray-org&t_u=https%3A%2F%2Fjasonmurray.org%2Fposts%2F2021%2Fmonitorwireless%2F&t_d=Monitoring%20layer-1%20wireless%20traffic%20with%20Linux&t_t=Monitoring%20layer-1%20wireless%20traffic%20with%20Linux&s_o=default#version=d92e3c2dd4530beaef2329d8676faf04
https://jasonmurray-org.disqus.com/embed.js
https://referrer.disqus.com/juggler/event.gif?abe=0&embed_hidden=0&load_time=1142&event=init_embed&thread=8387161190&forum=jasonmurray-org&forum_id=4689946&imp=2dfqc27umsm7f&prev_imp&thread_slug=monitoring_layer_1_wireless_traffic_with_linux&user_type=anon&referrer=https%3A%2F%2Fjasonmurray.org&theme=next&dnt=0&tracking_enabled=0&experiment=network_default_hidden&variant=fallthrough&service=dynamic&promoted_enabled=false&max_enabled=false
https://ssl.gstatic.com/accounts/o/3920524159-idpiframe.js
https://www.facebook.com/x/oauth/status?ancestor_origins=https%3A%2F%2Fjasonmurray.org&client_id=52254943976&input_token&origin=1&redirect_uri=https%3A%2F%2Fdisqus.com%2Fembed%2Fcomments%2F%3Fbase%3Ddefault%26f%3Djasonmurray-org%26t_u%3Dhttps%253A%252F%252Fjasonmurray.org%252Fposts%252F2021%252Fmonitorwireless%252F%26t_d%3DMonitoring%2520layer-1%2520wireless%2520traffic%2520with%2520Linux%26t_t%3DMonitoring%2520layer-1%2520wireless%2520traffic%2520with%2520Linux%26s_o%3Ddefault%23version%3Dd92e3c2dd4530beaef2329d8676faf04&sdk=joey&wants_cookie_data=false
```


To resolve these unnecessary privacy concerns, I removed the Disqus comment service from my site:

```
# disqusShortname = "jasonmurray-org"
```

There are now 4 requests per page load.  They all stay local to my site:

![Image of Safari dev tool showing only 4 requests page load](/images/2021-02-06-15-08-34.png)

