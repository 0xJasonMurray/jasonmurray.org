---
title: "Force HTTPS connections with HSTS"
date: 2021-02-02T04:33:14-06:00
toc: false
images:
categories:
  - tech
tags: 
  - tls
  - hsts
  - https
---

# Overview

HTTP Strict Transport Security (HSTS) is a mechanism web servers issue to signal clients an `https` connection is mandatory in order to fetch content. HSTS helps to protect websites against man-in-the-middle techniques such as protocol downgrading or cookie hijacking.

The HSTS policy is communicated to clients by injecting a `Strict-Transport-Security` header in the `http` response from the web server.  The header includes the minimum number of seconds the client must honor the policy.  

**NOTE:** Caution should be exercised prior to enabling HSTS.  If the web server is unable to communication over an `https` connections, clients will no longer be capable of fetching content until the user manually clears the HSTS cache or the max age timer expires.


# Details

Prior to enabling HSTS, the client does not have a HSTS policy:

![Image of Safari web browser displaying domain policy settings](/images/2021-01-31-14-35-32.png)

The web server does not issue a HSTP policy:

```
jemurray@Jasons-MacBook-Pro:~ $ wget --spider --server-response jasonmurray.org 2>&1 | grep Strict-Transport
  # <nothing displayed>
jemurray@Jasons-MacBook-Pro:~ $
```

My site, `jasonmurray.org`, is front-ended by the Cloudflare CDN.  To enable HSTS, use the Cloudflare control panel:

![Image of Cloudflare HSTS Enable button](/images/2021-01-31-14-36-18.png)

After clicking `Enable HSTS` Cloudflare displays this warning.  As noted earlier, it is important to understand potential issues with HSTS:

> HTTP Strict Transport Security (HSTS) can substantially improve the security of your website. However, there are important considerations to keep in mind when enabling HSTS:
>
> HTTPS (SSL) must be enabled in order to use HSTS.
>
> - If you turn on HSTS and do not have HTTPS for your website, browsers will not accept the HSTS setting.
> - If you have HSTS enabled and leave Cloudflare, you need to continue to support HTTPS through a new service provider otherwise your site will become inaccessible to visitors until you support HTTPS again.
> - If you turn off Cloudflare’s HTTPS while HSTS is enabled, and you don’t have a valid SSL certificate on your origin server, your website will become inaccessible to visitors.
> 
> Note: Disabling Cloudflare’s HTTP can be done in several ways: Grey clouding a subdomain in your DNS records, > “Pausing” the Cloudflare service, or having a misconfigured custom SSL certificate through your Cloudflare > dashboard (e.g., invalid SSL certificates, expired certificates, or mismatched host names).
> 
> If you need to disable HTTPS on your domain, you must first disable HSTS in your Cloudflare dashboard and > wait for the max-age to lapse to guarantee that every browser is aware of this change before you can disable  HTTPS. The average max-age is six months (you can set the max-age in the next step). If you remove HTTPS > before disabling HSTS your website will become inaccessible to visitors for up to the max-age or until you > support HTTPS again. Because disabling HTTPS on an HSTS enabled website can have these consequences, we > strongly suggest that you have a committed HTTPS service in place before enabling this feature.

*Alternative Method:* For sites that don't use Cloudflare, most web servers offer a way to inject custom headers in the `http` response.  For example, add this line to the `nginx` configuration:

```
add_header Strict-Transport-Security "max-age=31536000;
```

Once HSTS is enabled, reload or browse to the website.  The website data cache in the browser will now contain the new policy:

![Image of Safari web browser with HSTS policy for jasonmurray.org](/images/2021-01-31-14-44-09.png)

To confirm `Strict-Transport-Security` is set properly, use `wget` to examine the HSTS policy header:

```
jemurray@Jasons-MacBook-Pro:~ $ wget --spider --server-response jasonmurray.org 2>&1 | grep Strict-Transport
  Strict-Transport-Security: max-age=15552000
jemurray@Jasons-MacBook-Pro:~ $
```