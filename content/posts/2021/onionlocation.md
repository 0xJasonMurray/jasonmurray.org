---
title: "Configuring nginx to use the TOR Onion-Location header"
date: 2021-01-29T04:13:37-06:00
toc: false
images:
categories:
  - tech
tags: 
  - tor
  - onion-location
  - nginx
---

# Overview

I [recently added https://jasonmurray.org/](/posts/2021/jmtor/) behind the [Onion Service Protocol](https://2019.www.torproject.org/docs/onion-services), formally known as TOR hidden servers.   As noted in the post, my objective was not to hide the server, it does have my full name in the domain name after all, but to give visitors a way to remain anonymous while browsing my site.

If a user is attached to the Tor network and their browser supports the `Onion-location` headers, I am able to automatically redirect any attempts to access `https://jasonmurray.org/` to the corresponding `.onion` URL.

This post details the configuration necessary to enable `Onion-location` redirection with a Tor browser.  


# Details

Server information: `jasonmurray.org` runs on a Debian 10 server with a `nginx` web server.  


Add the following line to the `ngix` server configuration.  The onion URL should be replaced with the one assigned specifically to your server:

```
add_header Onion-Location http://trspv4gsa5irkrflbskyzwfo6vsj5h6i6zaelgc52hxmuoz6w6xpzbid.onion$request_uri;
```

Restart `nginx` and use `wget` to validate the new header is returned:

```
jemurray@jasons-mbp:~ $ wget --server-response --spider jasonmurray.org
Spider mode enabled. Check if remote file exists.
--2021-01-24 20:12:53--  http://jasonmurray.org/
Resolving jasonmurray.org (jasonmurray.org)... 2606:4700:3037::6815:2939, 2606:4700:3033::ac43:bd75, 104.21.41.57, ...
Connecting to jasonmurray.org (jasonmurray.org)|2606:4700:3037::6815:2939|:80... connected.
HTTP request sent, awaiting response...
  HTTP/1.1 301 Moved Permanently
  Date: Mon, 25 Jan 2021 02:12:55 GMT
  Connection: keep-alive
  Cache-Control: max-age=3600
  Expires: Mon, 25 Jan 2021 03:12:55 GMT
  Location: https://jasonmurray.org/
  cf-request-id: 07d8e9d8fa000003ccb7a7b000000001
  Report-To: {"endpoints":[{"url":"https:\\/\\/a.nel.cloudflare.com\\/report?s=exMIBw304A9kEMXcDrYKchpe7TbeY0MJ9O10odCZ6aoaMtZ1ldvCl01L%2BOmGyXNaSUmHgPlPjYU%2FeN7IY9IxdwQgKd%2Fw6jeQcTwrC5gC%2BeM%2Fm92Jg3NWJ7MVAsE%3D"}],"max_age":604800,"group":"cf-nel"}
  NEL: {"report_to":"cf-nel","max_age":604800}
  Vary: Accept-Encoding
  Server: cloudflare
  CF-RAY: 616e78d4cf1203cc-ORD
Location: https://jasonmurray.org/ [following]
Spider mode enabled. Check if remote file exists.
--2021-01-24 20:12:55--  https://jasonmurray.org/
Connecting to jasonmurray.org (jasonmurray.org)|2606:4700:3037::6815:2939|:443... connected.
HTTP request sent, awaiting response...
  HTTP/1.1 200 OK
  Date: Mon, 25 Jan 2021 02:12:55 GMT
  Content-Type: text/html
  Connection: keep-alive
  Set-Cookie: __cfduid=d9fe758acb5e9a2ab5ad6e3d3e6ee959c1611540775; expires=Wed, 24-Feb-21 02:12:55 GMT; path=/; domain=.jasonmurray.org; HttpOnly; SameSite=Lax; Secure
  Last-Modified: Mon, 25 Jan 2021 00:51:56 GMT
  Onion-Location: http://trspv4gsa5irkrflbskyzwfo6vsj5h6i6zaelgc52hxmuoz6w6xpzbid.onion/
  CF-Cache-Status: DYNAMIC
  cf-request-id: 07d8e9daca0000033f1abb1000000001
  Expect-CT: max-age=604800, report-uri="https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct"
  Report-To: {"endpoints":[{"url":"https:\\/\\/a.nel.cloudflare.com\\/report?s=VS0UP44GjTeC%2BVayGt0h3Yc0lBY6VavmTnKZ0DXbt5zaZGJL5U8dVG6sHdPW9GxhGEpFmD7q9umiQjWh%2BVJ8Gup8f5isCt0rc13fIOQth4vhOtrsqYgMYZ3STqQ%3D"}],"max_age":604800,"group":"cf-nel"}
  NEL: {"report_to":"cf-nel","max_age":604800}
  Server: cloudflare
  CF-RAY: 616e78d7ae83033f-ORD
Length: unspecified [text/html]
Remote file exists and could contain further links,
but recursion is disabled -- not retrieving.
```

Anyone browsing to `https://jasonmurray.org` with a Tor browser will be automatically redirected to the `.onion` URL:

![Image of a graphical web browser with the URL https://jasonmurray.org](/images/2021-01-24-20-19-06.png)

When the browser detects a `Onion-location` header, a purple box will appear in the URL bar stating `.onion available` - Click on the box to go to the `.onion` version of the website.

![Image of a purple colored box in the URL box of a graphical web browser indicating the non-onion website has a onion site available](/images/2021-01-24-20-22-28.png)


The browser can be configured to automatically prefer the `.onion` URL.  Edit the `Privacy & Security` preference settings:

![Image of the settings within the Tor browser to set automation redirection to onion enabled websites](/images/2021-01-24-20-24-11.png)

Now browsing a website which sets the `Onion-Location:` header will automatically redirect:

![Image of the graphical Tor browser automatically redirecting to the onion site](/images/2021-01-24-20-23-25.png)