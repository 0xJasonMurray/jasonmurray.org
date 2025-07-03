---
title: "Using openssl to connect to SSL enabled services"
date: 2020-09-12T16:16:00-05:00
toc: false
images:
categories:
  - tech
tags: 
  - openssl
  - commandline
---

# Overview

When I need to make a connection to an SSL enabled service or validate SSL certificate information, I use the `openssl` command.

This command allows me to connect to any SSL enabled service and validate key SSL certificate information, all within a single command.   On most unix systems `openssl` is installed by default, and is considered by many to be the swiss army knife of SSL tools.

# Summary of common OpenSSL commands:

Web server:

```
openssl s_client -connect jasonmurray.org:443 -servername jasonmurray.org
```

TTLS enabled SMTP server:

```
openssl s_client -starttls smtp -connect smtp.gmail.com:587
```

SSL enabled SMTP server:

```
openssl s_client -connect smtp.gmail.com:465
```

# OpenSSL: The Web Server

This is one of the most common uses for the `openssl` connect command.  I find the two primary reasons are:

* Making a connection to an SSL enable server.
* Validating information in the SSL certificate such as date, issuer, OU, CN, or SNA attributes.

Connect to the SSL enable web server:

```
openssl s_client -connect jasonmurray.org:443 -servername jasonmurray.org
```

Then type the following 2 lines and press `<enter>` twice:

```
GET / HTTP/1.0
Host: jasonmurray.org

```

The full output will look something like this:

```
jemurray@shell:~$ openssl s_client -connect jasonmurray.org:443 -servername jasonmurray.org
CONNECTED(00000003)
depth=2 C = IE, O = Baltimore, OU = CyberTrust, CN = Baltimore CyberTrust Root
verify return:1
depth=1 C = US, O = "Cloudflare, Inc.", CN = Cloudflare Inc ECC CA-3
verify return:1
depth=0 C = US, ST = CA, L = San Francisco, O = "Cloudflare, Inc.", CN = sni.cloudflaressl.com
verify return:1
---
Certificate chain
 0 s:C = US, ST = CA, L = San Francisco, O = "Cloudflare, Inc.", CN = sni.cloudflaressl.com
   i:C = US, O = "Cloudflare, Inc.", CN = Cloudflare Inc ECC CA-3
 1 s:C = US, O = "Cloudflare, Inc.", CN = Cloudflare Inc ECC CA-3
   i:C = IE, O = Baltimore, OU = CyberTrust, CN = Baltimore CyberTrust Root
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIEyTCCBG+gAwIBAgIQBH9jmqXehcFq7yaFJsSUTjAKBggqhkjOPQQDAjBKMQsw
CQYDVQQGEwJVUzEZMBcGA1UEChMQQ2xvdWRmbGFyZSwgSW5jLjEgMB4GA1UEAxMX
Q2xvdWRmbGFyZSBJbmMgRUNDIENBLTMwHhcNMjAwODE3MDAwMDAwWhcNMjEwODE3
MTIwMDAwWjBtMQswCQYDVQQGEwJVUzELMAkGA1UECBMCQ0ExFjAUBgNVBAcTDVNh
biBGcmFuY2lzY28xGTAXBgNVBAoTEENsb3VkZmxhcmUsIEluYy4xHjAcBgNVBAMT
FXNuaS5jbG91ZGZsYXJlc3NsLmNvbTBZMBMGByqGSM49AgEGCCqGSM49AwEHA0IA
BMg6qVutwhwbJkLy3rDqC3tfyrfzIv3wJNcYzH90q5OZnHrcyYPqegR1rksnk5x+
jrHiPpY5kcekqFexrvBY4C+jggMSMIIDDjAfBgNVHSMEGDAWgBSlzjfq67B1DpRn
iLRF+tkkEIeWHzAdBgNVHQ4EFgQUxu5/wY5+xYkGgFqucXH4BARC38gwRAYDVR0R
BD0wO4IVc25pLmNsb3VkZmxhcmVzc2wuY29tghEqLmphc29ubXVycmF5Lm9yZ4IP
amFzb25tdXJyYXkub3JnMA4GA1UdDwEB/wQEAwIHgDAdBgNVHSUEFjAUBggrBgEF
BQcDAQYIKwYBBQUHAwIwewYDVR0fBHQwcjA3oDWgM4YxaHR0cDovL2NybDMuZGln
aWNlcnQuY29tL0Nsb3VkZmxhcmVJbmNFQ0NDQS0zLmNybDA3oDWgM4YxaHR0cDov
L2NybDQuZGlnaWNlcnQuY29tL0Nsb3VkZmxhcmVJbmNFQ0NDQS0zLmNybDBMBgNV
HSAERTBDMDcGCWCGSAGG/WwBATAqMCgGCCsGAQUFBwIBFhxodHRwczovL3d3dy5k
aWdpY2VydC5jb20vQ1BTMAgGBmeBDAECAjB2BggrBgEFBQcBAQRqMGgwJAYIKwYB
BQUHMAGGGGh0dHA6Ly9vY3NwLmRpZ2ljZXJ0LmNvbTBABggrBgEFBQcwAoY0aHR0
cDovL2NhY2VydHMuZGlnaWNlcnQuY29tL0Nsb3VkZmxhcmVJbmNFQ0NDQS0zLmNy
dDAMBgNVHRMBAf8EAjAAMIIBBAYKKwYBBAHWeQIEAgSB9QSB8gDwAHYA9lyUL9F3
MCIUVBgIMJRWjuNNExkzv98MLyALzE7xZOMAAAFz+0pmvgAABAMARzBFAiEA+hFd
VTTQyzpLZFw/pyWZB3tdhA5JEuY75O4sUett5CkCIDiVOuOqYLu37tkSysQUITo5
l2QaWc5AnKcLXszfj3EaAHYAXNxDkv7mq0VEsV6a1FbmEDf71fpH3KFzlLJe5vbH
DsoAAAFz+0pm/gAABAMARzBFAiEA+h5csm2YfqcFqM9btoIE4LVpGdsN2FkNvuPC
HT13+UkCICaViEgeD8Zoalsrc+K0DdQuahMALY/VMR26BFBE+1p8MAoGCCqGSM49
BAMCA0gAMEUCIECTc9fdi74d1YtwVtmg5w1OIg2r8xVt3Hdn4RP7ABybAiEA6dwn
ogfzxwkITjIRs2Qx09HDNvZTxBqF3NpkZOJPrnk=
-----END CERTIFICATE-----
subject=C = US, ST = CA, L = San Francisco, O = "Cloudflare, Inc.", CN = sni.cloudflaressl.com

issuer=C = US, O = "Cloudflare, Inc.", CN = Cloudflare Inc ECC CA-3

---
No client certificate CA names sent
Peer signing digest: SHA256
Peer signature type: ECDSA
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 2521 bytes and written 387 bytes
Verification: OK
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Server public key is 256 bit
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 0 (ok)
---
GET / HTTP/1.0
Host: jasonmurray.org

---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 0188F2A5333CF4D794843DB6F7859B4EC99535A07F900BBE287A52035FB03609
    Session-ID-ctx:
    Resumption PSK: 37739394C1F780077DEF13668C543475AB2770F9D3AA6CA757A107E6C2B3BD0CA2EB48C5301D934F2435A43D1F88C962
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 64800 (seconds)
    TLS session ticket:
    0000 - b1 fc 5e 40 e3 4a 5d 5e-a3 da ab 27 83 36 dd 6c   ..^@.J]^...'.6.l
    0010 - 9c 99 ca cf 86 31 b5 41-ea a3 53 fa 39 cb 73 4b   .....1.A..S.9.sK
    0020 - cb 5a 3e 38 ab c4 66 b9-d5 91 1c c8 92 46 cd e2   .Z>8..f......F..
    0030 - b8 5c 17 ed de aa 68 d4-fb 82 2d cc 26 35 82 1a   .\....h...-.&5..
    0040 - 6c 6e d1 ab 81 78 64 68-df fd 93 d4 6a 33 53 79   ln...xdh....j3Sy
    0050 - 4c 6a 53 d3 c8 74 47 bb-8c a1 bb 82 15 3b 2f 96   LjS..tG......;/.
    0060 - 58 37 8b d5 63 2a 08 19-31 36 7c d4 e0 af e9 5c   X7..c*..16|....\
    0070 - f5 67 fa c5 50 55 b1 f3-00 5e 45 19 b4 27 40 56   .g..PU...^E..'@V
    0080 - ec a6 2c 2b 0d 21 b6 db-5d de e7 76 31 cf 37 d6   ..,+.!..]..v1.7.
    0090 - 09 a0 87 41 41 26 83 1b-ab ce 1f 4c 3c d4 df 07   ...AA&.....L<...
    00a0 - de 4e da 75 c5 3b a2 4e-d7 6c 51 e0 1d 9e 3e 16   .N.u.;.N.lQ...>.
    00b0 - 09 cb 07 fd 01 fc c8 91-b7 4f e4 0a d1 c4 9e 65   .........O.....e

    Start Time: 1599945321
    Timeout   : 7200 (sec)
    Verify return code: 0 (ok)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 605F9608800FFC0F0C626A82309D621EFD72001A717C626329BAAB2AA3E8CBFE
    Session-ID-ctx:
    Resumption PSK: E87DED20218DD5DA87F7392748E07970B9271D2EBA510C6B84022E73C2D0849156B1C7CCD734FCAE72CB40DD9B546959
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 64800 (seconds)
    TLS session ticket:
    0000 - b1 fc 5e 40 e3 4a 5d 5e-a3 da ab 27 83 36 dd 6c   ..^@.J]^...'.6.l
    0010 - e7 87 8c 30 7c ad 79 e5-11 33 a5 21 32 00 ef 0e   ...0|.y..3.!2...
    0020 - 55 18 44 59 05 af ea 7a-99 cf 91 eb b6 c2 21 43   U.DY...z......!C
    0030 - 14 97 59 ba c0 b4 ee 62-31 56 7f 1d ba 2f d6 84   ..Y....b1V.../..
    0040 - c5 71 2a 02 8f cc 30 67-b5 54 60 cc a6 b2 59 fa   .q*...0g.T`...Y.
    0050 - 67 bc b6 20 64 bc 3e 22-59 77 df 30 4d 93 5f ae   g.. d.>"Yw.0M._.
    0060 - 75 bc 83 85 b7 d7 92 78-8a 98 8b d6 d1 ed b5 fa   u......x........
    0070 - 93 50 75 86 d2 f3 58 eb-04 90 12 48 91 51 f2 c1   .Pu...X....H.Q..
    0080 - 80 db 2a 66 af 90 71 e3-50 2d 5d de be 81 65 58   ..*f..q.P-]...eX
    0090 - 07 e5 ce e2 9a 1f 21 10-5f 33 ee 0c b7 4b fa ed   ......!._3...K..
    00a0 - 09 31 0b cd 4b 88 75 63-04 32 01 e1 0d 92 03 25   .1..K.uc.2.....%
    00b0 - 0a ac 83 87 be d7 45 4d-d3 0e 06 02 1c 9f 78 1b   ......EM......x.

    Start Time: 1599945321
    Timeout   : 7200 (sec)
    Verify return code: 0 (ok)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
HTTP/1.1 200 OK
Date: Sat, 12 Sep 2020 21:15:21 GMT
Content-Type: text/html
Connection: close
Set-Cookie: __cfduid=d24413e8ef5a47cfe2c31f1aabf0eea401599945321; expires=Mon, 12-Oct-20 21:15:21 GMT; path=/; domain=.jasonmurray.org; HttpOnly; SameSite=Lax; Secure
Last-Modified: Fri, 11 Sep 2020 15:29:33 GMT
CF-Cache-Status: DYNAMIC
cf-request-id: 0525c545fb0000e6d8b0121200000001
Expect-CT: max-age=604800, report-uri="https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct"
Server: cloudflare
CF-RAY: 5d1ca4b2ed96e6d8-EWR

<!DOCTYPE html>
<html lang="en-us">

<head>
	<meta name="generator" content="Hugo 0.74.3" />
	<meta charset="UTF-8">
	<meta name="viewport" content="width=device-width, initial-scale=1.0">
	<meta http-equiv="X-UA-Compatible" content="ie=edge">
	<meta name="theme-color" content="#494f5c">
	<meta name="msapplication-TileColor" content="#494f5c">
<meta itemprop="name" content="Jason Murray">
<meta itemprop="description" content="Personal website of Jason Murray, Internet Architect, Consultant, and Entrepreneur">
<meta property="og:title" content="Jason Murray" />
<meta property="og:description" content="Personal website of Jason Murray, Internet Architect, Consultant, and Entrepreneur" />
<meta property="og:type" content="website" />
<meta property="og:url" content="https://jasonmurray.org/" />
<meta property="og:updated_time" content="2020-09-11T08:43:02-05:00" />
<meta name="twitter:card" content="summary"/>
<meta name="twitter:title" content="Jason Murray"/>
<meta name="twitter:description" content="Personal website of Jason Murray, Internet Architect, Consultant, and Entrepreneur"/>

	<link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png">
	<link rel="icon" type="image/png" sizes="32x32" href="/favicon-32x32.png">
	<link rel="icon" type="image/png" sizes="16x16" href="/favicon-16x16.png">
	<link rel="manifest" href="/site.webmanifest">
	<link rel="mask-icon" href="/safari-pinned-tab.svg" color="">
	<link rel="shortcut icon" href="/favicon.ico">

	<title>Jason Murray</title>
	<link rel="alternate" type="application/rss+xml" href="https://jasonmurray.org/index.xml" title="Jason Murray" />
	<link rel="stylesheet" href="https://jasonmurray.org/css/style.min.657bcb7af31123e4156b1a3d2ff60a636717e54ead74f882136b5114cf72b55e.css" integrity="sha256-ZXvLevMRI+QVaxo9L/YKY2cX5U6tdPiCE2tRFM9ytV4=" crossorigin="anonymous">

</head>

<body id="page">

	<div id="spotlight" class="animated fadeIn">
		<div id="home-center">
			<h1 id="home-title">Jason Murray</h1>
			<p id="home-subtitle">Internet Architect, Consultant, and Entrepreneur</p>
			<div id="home-social">
				<a href="/cdn-cgi/l/email-protection#117b747c6463637068516b6674727a3f7f7465" target="_blank" rel="noopener me" title="Email"><svg xmlns="http://www.w3.org/2000/svg" class="feather" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"></path><polyline points="22,6 12,13 2,6"></polyline></svg></a><a href="http://keys.gnupg.net/pks/lookup?search=0x6E8A4FCDF2F9138C&amp;fingerprint=on&amp;op=index" target="_blank" rel="noopener me" title="Email"><svg xmlns="http://www.w3.org/2000/svg" class="feather" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M4 4h16c1.1 0 2 .9 2 2v12c0 1.1-.9 2-2 2H4c-1.1 0-2-.9-2-2V6c0-1.1.9-2 2-2z"></path><polyline points="22,6 12,13 2,6"></polyline></svg></a><a href="https://github.com/0xJasonMurray/" target="_blank" rel="noopener me" title="Github"><svg xmlns="http://www.w3.org/2000/svg" class="feather" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M9 19c-5 1.5-5-2.5-7-3m14 6v-3.87a3.37 3.37 0 0 0-.94-2.61c3.14-.35 6.44-1.54 6.44-7A5.44 5.44 0 0 0 20 4.77 5.07 5.07 0 0 0 19.91 1S18.73.65 16 2.48a13.38 13.38 0 0 0-7 0C6.27.65 5.09 1 5.09 1A5.07 5.07 0 0 0 5 4.77a5.44 5.44 0 0 0-1.5 3.78c0 5.42 3.3 6.61 6.44 7A3.37 3.37 0 0 0 9 18.13V22"></path></svg></a><a href="https://twitter.com/0xJasonMurray/" target="_blank" rel="noopener me" title="Twitter"><svg xmlns="http://www.w3.org/2000/svg" class="feather" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M23 3a10.9 10.9 0 0 1-3.14 1.53 4.48 4.48 0 0 0-7.86 3v1A10.66 10.66 0 0 1 3 4s-4 9 5 13a11.64 11.64 0 0 1-7 2c9 5 20 0 20-11.5a4.5 4.5 0 0 0-.08-.83A7.72 7.72 0 0 0 23 3z"></path></svg></a><a href="https://www.linkedin.com/in/jemurray/" target="_blank" rel="noopener me" title="Linkedin"><svg xmlns="http://www.w3.org/2000/svg" class="feather" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M16 8a6 6 0 0 1 6 6v7h-4v-7a2 2 0 0 0-2-2 2 2 0 0 0-2 2v7h-4v-7a6 6 0 0 1 6-6z"></path><rect x="2" y="9" width="4" height="12"></rect><circle cx="4" cy="4" r="2"></circle></svg></a>
			</div>
			<nav id="home-nav" class="site-nav">

				<a href="https://jasonmurray.org/pages/about/">About</a>
				<a href="https://jasonmurray.org/posts/" class="has-children">Today I Shared</a>
				<span class="sub-menu">
					<a href="https://jasonmurray.org/categories/personal">Personal</a>
					<a href="https://jasonmurray.org/categories/tech">Tech</a>
					<a href="https://jasonmurray.org/categories/leadership">Leadership</a>
				</span>
				<a href="https://jasonmurray.org/pages/resume">Resume</a>
				<a href="https://jasonmurray.org/tags">Tags</a>

			</nav>
		</div>
		<div id="home-footer">
			<p>
				&copy; 2020 <a href="https://jasonmurray.org/">Jason Murray</a> &#183; <a href="https://creativecommons.org/licenses/by-nc/4.0/" target="_blank" rel="noopener">CC BY-NC 4.0</a> &#183; <a href="https://jasonmurray.org/posts/index.xml" target="_blank" title="rss"><svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round" class="feather feather-rss"><path d="M4 11a9 9 0 0 1 9 9"></path><path d="M4 4a16 16 0 0 1 16 16"></path><circle cx="5" cy="19" r="1"></circle></svg></a>
			</p>
		</div>
	</div>


	<script data-cfasync="false" src="/cdn-cgi/scripts/5c5dd728/cloudflare-static/email-decode.min.js"></script><script src="https://jasonmurray.org/js/bundle.min.4a9a0ac3d2217822c7865b4161e6c2a71de1d70492264337755427898dd718f6.js" integrity="sha256-SpoKw9IheCLHhltBYebCpx3h1wSSJkM3dVQniY3XGPY=" crossorigin="anonymous"></script>


</body>

</html>
read:errno=0
```

# OpenSSL: TTLS SMTP

Connect to a TTLS enabled SMTP server:

```
openssl s_client -starttls smtp -connect smtp.gmail.com:587
```

The full output looks like this:

```
jemurray@shell:~$ openssl s_client -starttls smtp -connect smtp.gmail.com:587
CONNECTED(00000003)
depth=2 OU = GlobalSign Root CA - R2, O = GlobalSign, CN = GlobalSign
verify return:1
depth=1 C = US, O = Google Trust Services, CN = GTS CA 1O1
verify return:1
depth=0 C = US, ST = California, L = Mountain View, O = Google LLC, CN = smtp.gmail.com
verify return:1
---
Certificate chain
 0 s:C = US, ST = California, L = Mountain View, O = Google LLC, CN = smtp.gmail.com
   i:C = US, O = Google Trust Services, CN = GTS CA 1O1
 1 s:C = US, O = Google Trust Services, CN = GTS CA 1O1
   i:OU = GlobalSign Root CA - R2, O = GlobalSign, CN = GlobalSign
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIEyTCCA7GgAwIBAgIRAJrPpLI0oZfCAgAAAAB5ZFkwDQYJKoZIhvcNAQELBQAw
QjELMAkGA1UEBhMCVVMxHjAcBgNVBAoTFUdvb2dsZSBUcnVzdCBTZXJ2aWNlczET
MBEGA1UEAxMKR1RTIENBIDFPMTAeFw0yMDA4MjYwODEyNTFaFw0yMDExMTgwODEy
NTFaMGgxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQH
Ew1Nb3VudGFpbiBWaWV3MRMwEQYDVQQKEwpHb29nbGUgTExDMRcwFQYDVQQDEw5z
bXRwLmdtYWlsLmNvbTBZMBMGByqGSM49AgEGCCqGSM49AwEHA0IABNg3aTMBeiTA
O0rrLRChnguMwDAM5ZUpfYvOEGeAkRVfI2C/UVsS3GZ8oa04oVKKt4dlz2zbRXLc
UiZWnCXdcC+jggJdMIICWTAOBgNVHQ8BAf8EBAMCB4AwEwYDVR0lBAwwCgYIKwYB
BQUHAwEwDAYDVR0TAQH/BAIwADAdBgNVHQ4EFgQUCuwtM6w8U31G+wCQx0hCSw7V
v/swHwYDVR0jBBgwFoAUmNH4bhDrz5vsYJ8YkBug630J/SswaAYIKwYBBQUHAQEE
XDBaMCsGCCsGAQUFBzABhh9odHRwOi8vb2NzcC5wa2kuZ29vZy9ndHMxbzFjb3Jl
MCsGCCsGAQUFBzAChh9odHRwOi8vcGtpLmdvb2cvZ3NyMi9HVFMxTzEuY3J0MBkG
A1UdEQQSMBCCDnNtdHAuZ21haWwuY29tMCEGA1UdIAQaMBgwCAYGZ4EMAQICMAwG
CisGAQQB1nkCBQMwMwYDVR0fBCwwKjAooCagJIYiaHR0cDovL2NybC5wa2kuZ29v
Zy9HVFMxTzFjb3JlLmNybDCCAQUGCisGAQQB1nkCBAIEgfYEgfMA8QB3ALIeBcyL
os2KIE6HZvkruYolIGdr2vpw57JJUy3vi5BeAAABdCoKm74AAAQDAEgwRgIhAJxE
Qm11aEkoGEvY6L/EqfX1ibUWExyBDsFkN9Ye4GWOAiEAlHoxQ64b3WNVAL5E3vvR
QN3idEVT/aRjxYIYBEQZFikAdgDGUqDsSM6z/KsXCZLEOodBMwnoAGWiYlJAG6M2
KhfFZQAAAXQqCpvQAAAEAwBHMEUCIQCTpczWzpMx7REOsVGKT75FyXO4tS3znTsf
cS5gfbL54wIgfiKfwnfJNGM8BZjINFH1VG8R7nqDsJP4UC7LQxI8OhMwDQYJKoZI
hvcNAQELBQADggEBAK7HqfD+/qFWdzJqm8SfoK/V1i9KBNcerDD+PoqlbIedHQnO
sIuIkGMdrZed/ynzvcn+co5IxmfiIe3nWHQfolYsw35z1+cTsAmV25Jz0WLLswOT
UbefRyfbw4X3h9GQduRmUssmBgmwHfjFXEt84rZf5pWWKZkfbgrUjwhj4XsWUCo4
DIzGGrhRw7xOAkGq/wZIIVF4j3ed5/76WSFW+qsC1b5toj+kRfXKxnUcfSx/K9/b
GXx40K9O1tL51sPYgvaIVQ/Mnl4KC6pknmFMpfDSSrP1HqUlqdA5tTprWTIHRlEx
uUPvofgGCQ95lLUJVrtVCtLC9+mpwi9a3vmK6NA=
-----END CERTIFICATE-----
subject=C = US, ST = California, L = Mountain View, O = Google LLC, CN = smtp.gmail.com

issuer=C = US, O = Google Trust Services, CN = GTS CA 1O1

---
No client certificate CA names sent
Peer signing digest: SHA256
Peer signature type: ECDSA
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 2906 bytes and written 419 bytes
Verification: OK
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Server public key is 256 bit
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 0 (ok)
---
250 SMTPUTF8
```

# OpenSSL: SSL Enabled SMTP

Connect to an SSL enabled SMTP server:

```
openssl s_client -connect smtp.gmail.com:465
```

The output looks like this:

```
jemurray@shell:~$ openssl s_client -connect smtp.gmail.com:465
CONNECTED(00000003)
depth=2 OU = GlobalSign Root CA - R2, O = GlobalSign, CN = GlobalSign
verify return:1
depth=1 C = US, O = Google Trust Services, CN = GTS CA 1O1
verify return:1
depth=0 C = US, ST = California, L = Mountain View, O = Google LLC, CN = smtp.gmail.com
verify return:1
---
Certificate chain
 0 s:C = US, ST = California, L = Mountain View, O = Google LLC, CN = smtp.gmail.com
   i:C = US, O = Google Trust Services, CN = GTS CA 1O1
 1 s:C = US, O = Google Trust Services, CN = GTS CA 1O1
   i:OU = GlobalSign Root CA - R2, O = GlobalSign, CN = GlobalSign
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIEyTCCA7GgAwIBAgIRAJrPpLI0oZfCAgAAAAB5ZFkwDQYJKoZIhvcNAQELBQAw
QjELMAkGA1UEBhMCVVMxHjAcBgNVBAoTFUdvb2dsZSBUcnVzdCBTZXJ2aWNlczET
MBEGA1UEAxMKR1RTIENBIDFPMTAeFw0yMDA4MjYwODEyNTFaFw0yMDExMTgwODEy
NTFaMGgxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQH
Ew1Nb3VudGFpbiBWaWV3MRMwEQYDVQQKEwpHb29nbGUgTExDMRcwFQYDVQQDEw5z
bXRwLmdtYWlsLmNvbTBZMBMGByqGSM49AgEGCCqGSM49AwEHA0IABNg3aTMBeiTA
O0rrLRChnguMwDAM5ZUpfYvOEGeAkRVfI2C/UVsS3GZ8oa04oVKKt4dlz2zbRXLc
UiZWnCXdcC+jggJdMIICWTAOBgNVHQ8BAf8EBAMCB4AwEwYDVR0lBAwwCgYIKwYB
BQUHAwEwDAYDVR0TAQH/BAIwADAdBgNVHQ4EFgQUCuwtM6w8U31G+wCQx0hCSw7V
v/swHwYDVR0jBBgwFoAUmNH4bhDrz5vsYJ8YkBug630J/SswaAYIKwYBBQUHAQEE
XDBaMCsGCCsGAQUFBzABhh9odHRwOi8vb2NzcC5wa2kuZ29vZy9ndHMxbzFjb3Jl
MCsGCCsGAQUFBzAChh9odHRwOi8vcGtpLmdvb2cvZ3NyMi9HVFMxTzEuY3J0MBkG
A1UdEQQSMBCCDnNtdHAuZ21haWwuY29tMCEGA1UdIAQaMBgwCAYGZ4EMAQICMAwG
CisGAQQB1nkCBQMwMwYDVR0fBCwwKjAooCagJIYiaHR0cDovL2NybC5wa2kuZ29v
Zy9HVFMxTzFjb3JlLmNybDCCAQUGCisGAQQB1nkCBAIEgfYEgfMA8QB3ALIeBcyL
os2KIE6HZvkruYolIGdr2vpw57JJUy3vi5BeAAABdCoKm74AAAQDAEgwRgIhAJxE
Qm11aEkoGEvY6L/EqfX1ibUWExyBDsFkN9Ye4GWOAiEAlHoxQ64b3WNVAL5E3vvR
QN3idEVT/aRjxYIYBEQZFikAdgDGUqDsSM6z/KsXCZLEOodBMwnoAGWiYlJAG6M2
KhfFZQAAAXQqCpvQAAAEAwBHMEUCIQCTpczWzpMx7REOsVGKT75FyXO4tS3znTsf
cS5gfbL54wIgfiKfwnfJNGM8BZjINFH1VG8R7nqDsJP4UC7LQxI8OhMwDQYJKoZI
hvcNAQELBQADggEBAK7HqfD+/qFWdzJqm8SfoK/V1i9KBNcerDD+PoqlbIedHQnO
sIuIkGMdrZed/ynzvcn+co5IxmfiIe3nWHQfolYsw35z1+cTsAmV25Jz0WLLswOT
UbefRyfbw4X3h9GQduRmUssmBgmwHfjFXEt84rZf5pWWKZkfbgrUjwhj4XsWUCo4
DIzGGrhRw7xOAkGq/wZIIVF4j3ed5/76WSFW+qsC1b5toj+kRfXKxnUcfSx/K9/b
GXx40K9O1tL51sPYgvaIVQ/Mnl4KC6pknmFMpfDSSrP1HqUlqdA5tTprWTIHRlEx
uUPvofgGCQ95lLUJVrtVCtLC9+mpwi9a3vmK6NA=
-----END CERTIFICATE-----
subject=C = US, ST = California, L = Mountain View, O = Google LLC, CN = smtp.gmail.com

issuer=C = US, O = Google Trust Services, CN = GTS CA 1O1

---
No client certificate CA names sent
Peer signing digest: SHA256
Peer signature type: ECDSA
Server Temp Key: X25519, 253 bits
---
SSL handshake has read 2641 bytes and written 386 bytes
Verification: OK
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Server public key is 256 bit
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 0 (ok)
---
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 0A5DBEEA0CA8E070F5D3788C2AC87C6A2C1CC333B62C4EEF3CD8B7680DF83450
    Session-ID-ctx:
    Resumption PSK: 0B5CF7F91E8824745518C889F9AF974D21194312E085E8240F966FEB37E0231D3DDEBEA67C4B2D0795F7BFA68A2300AC
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 172800 (seconds)
    TLS session ticket:
    0000 - 01 7d 6a e6 ce e8 46 28-07 81 88 3b a2 fa 99 51   .}j...F(...;...Q
    0010 - 64 19 ca bd f5 b9 d1 08-a1 f7 18 a0 32 89 f7 ff   d...........2...
    0020 - 87 71 2c 92 2d cf 61 95-6c d1 01 b1 c1 0f 1a 7e   .q,.-.a.l......~
    0030 - 43 d1 9c 62 a9 d3 21 d3-b5 47 8c 39 d1 6e e2 bb   C..b..!..G.9.n..
    0040 - 40 17 03 92 df a5 e7 18-23 47 cc c3 3e 7f b8 5d   @.......#G..>..]
    0050 - 23 72 67 91 0c ba fa 58-45 1b 14 b4 c4 5a 4a 0a   #rg....XE....ZJ.
    0060 - cb 58 d0 07 d9 89 63 5c-e0 0c 47 86 27 c5 a4 c8   .X....c\..G.'...
    0070 - b4 8a 0b 5c f8 11 a1 a7-40 45 e7 20 07 1b 2f 06   ...\....@E. ../.
    0080 - 45 a5 bb bb f2 9a 8c 51-c1 22 03 48 73 b1 95 eb   E......Q.".Hs...
    0090 - f7 2d fe 50 65 2e 8c 0d-4a 13 6a 2d 7e 87 66 08   .-.Pe...J.j-~.f.
    00a0 - d1 49 99 fe df f3 8e 5d-2d 6b 75 78 be ed 9a a0   .I.....]-kux....
    00b0 - fc 23 bf e5 c3 a1 02 8e-3e fa 61 07 e0 93 22 ef   .#......>.a...".
    00c0 - 62 d9 ab 20 e4 86 74 4e-db ac c8 8c a6 c5 28 16   b.. ..tN......(.
    00d0 - bc 50 e5 19 7e 27 9a fc-0a 15 41 66 eb bf ce 04   .P..~'....Af....
    00e0 - 7b 0b ca 4d ea ef 75 83-93 4d 88 ea               {..M..u..M..

    Start Time: 1599947087
    Timeout   : 7200 (sec)
    Verify return code: 0 (ok)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 9506E3647BB209BD43762E42DE09C1C6560018E35E49A6651094C7C9B1F2D5A2
    Session-ID-ctx:
    Resumption PSK: 17D27E2E7EDB2E07BB6136F9586F675645DDB64ADDA17C0548527E9E89AC01BC2DB6DF39A04ADE510E87A5287250B76F
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 172800 (seconds)
    TLS session ticket:
    0000 - 01 7d 6a e6 ce e8 46 28-07 81 88 3b a2 fa 99 51   .}j...F(...;...Q
    0010 - de 6d 9d a4 19 fe 5e 3f-22 af 1a ab e8 5a b3 ca   .m....^?"....Z..
    0020 - 7f b2 ae 7d dd 72 34 25-20 f7 e2 b6 2b b3 6c 6b   ...}.r4% ...+.lk
    0030 - 68 60 70 50 8f 24 e8 5f-b3 82 b9 0b 91 f4 82 a3   h`pP.$._........
    0040 - 74 67 0b 68 e8 fc ec 5d-6b fd 59 5f 36 52 34 d3   tg.h...]k.Y_6R4.
    0050 - 06 6e d2 37 1c 02 3e 56-bd 53 ad 39 de 93 ad 20   .n.7..>V.S.9...
    0060 - 9b 0c ba 75 be 51 e4 43-c0 60 12 b3 b2 e6 4d 7b   ...u.Q.C.`....M{
    0070 - d2 8e d1 7e 45 bf d8 b4-ad 55 13 36 8b ea 05 b2   ...~E....U.6....
    0080 - b0 a0 d6 f0 b9 22 a3 72-47 c9 94 d0 af d9 16 f7   .....".rG.......
    0090 - 40 e6 2b 9e 0b 71 19 8a-04 ec d7 8e 8f 3f d4 1f   @.+..q.......?..
    00a0 - e7 2c e6 e4 57 27 7c 9f-b3 90 92 86 53 ad ab 5c   .,..W'|.....S..\
    00b0 - 67 99 32 23 48 c1 7d e7-a0 06 21 0b de 74 0d d3   g.2#H.}...!..t..
    00c0 - 97 2a 75 2d ab bc 01 b3-8c 0d f6 4b 4b e0 8d 0a   .*u-.......KK...
    00d0 - a3 17 fa b2 53 bb d8 f5-8f 14 28 f2 99 e8 28 7b   ....S.....(...({
    00e0 - c3 62 a9 f8 f6 c6 97 cd-47 13 37 b3               .b......G.7.

    Start Time: 1599947087
    Timeout   : 7200 (sec)
    Verify return code: 0 (ok)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
220 smtp.gmail.com ESMTP b9sm8070255qtt.39 - gsmtp
```

