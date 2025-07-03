---
title: "Fake Phishing: Setup, detection, and take-down"
date: 2022-11-13T16:30:48-06:00
toc: false
images:
categories:
  - tech
tags: 
  - phishing
  - netcraft
  - microsoft
  - o365
description: It's easy and cheap to build and realistic Phishing site, but you might get a take down request from Microsoft.
---

# Overview

I'm teaching a class about Phishing. My goal is to explain with a real world example how inexpensive, easy to setup, and tricky it is tell a legitimate site from the real thing. To replicate the phishing site I bought a cheap domain, rented a VPS hosting server, setup DNS, and finally configured a phishing website using [Evilginx2](https://github.com/kgretzky/evilginx2).

Within 6 minutes of getting the site up and operational, DigitalOcean (who I host with) and NetCraft (on behalf of Microsoft) sent a cease-and-desist.

I thought it was pretty amazing to see how quickly this was detected.  

# Timeline

- Registered the domain: `logino365.click` at 2022-11-13T21:34:07.844Z
- Setup `Evilginx2` at 2022-11-13T21:56:15Z
- Received a Phishing complaint from DigitalOcean at 2022-11-13T22:02:42Z
- 6 minutes to detect and send a cease-and-desist - that is amazingly fast! Well done DigitalOcean, Netcraft, and Microsoft.

# Technical Details

## Register the Domain

I scrolled through the list of least expensive domains at the AWS registrar, until I found the `.click` domain for $3 per/year. After trying a few combinations of something that is similar to the Office 365 domain, I settled on `logino365.click`.  

## Setup the DNS Servers

To keep my other personal domains out of this project and prevent them from possibly getting wrapped up in a poor reputation for being associated with a "phishing" service, I used the DNS server built into Evilginx2 and setup glue records so the `logino365.click` can host it's own `ns` records. I like how AWS automatically detects the need for `glue` records:
  
  [![Image of AWS DNS configuration](/images/2022-11-13-16-46-18.png)](/images/2022-11-13-16-46-18.png)

## Evilginx2 Setup

- Download and compile Evilginx2:
  ```
  git clone https://github.com/kgretzky/evilginx2.git
  cd evilginx2
  make
  ```

- Start Evilginix2:
  ```
  ./bin/evilginx -p ./phishlets/
  ```

- Run through the configuration:

  ```
  : config

  domain              : logino365.click
  ip                  : 142.93.9.250
  redirect_key        : ar
  verification_key    : rs
  verification_token  : b584
  redirect_url        : https://www.youtube.com/watch?v=dQw4w9WgXcQ
  ```
- Configure the `phishlets`:
  
  ```
  : phishlets hostname o365 logino365.click
  [21:55:07] [inf] phishlet 'o365' hostname set to: logino365.click
  [21:55:07] [inf] disabled phishlet 'o365'
  : phishlets enable o365
  [21:56:02] [inf] enabled phishlet 'o365'
  [21:56:02] [inf] setting up certificates for phishlet 'o365'...
  [21:56:02] [inf] requesting SSL/TLS certificates from LetsEncrypt...
  [21:56:15] [+++] successfully set up SSL/TLS certificates for domains: [login.logino365.click www.logino365.click]
  ```

## Validate Site is Working

- Evilginx2 automatically generates a valid SSL certificate with Let's encrypt:

  [![Image of Evilginx2 Lets Encrypt SSL validation](/images/2022-11-13-16-51-06.png)](/images/2022-11-13-16-51-06.png)

- The site is up:

  [![Image of Office 365 Phishing site](/images/2022-11-13-16-49-11.png)](/images/2022-11-13-16-49-11.png)

## Six Minutes Later DigitalOcean Sends a Phishing Notice

- Netcraft Abuse Report

  ```
  Abuse Report

  Extracted Details

  ip 142.93.9.250
  send_date 2022-11-13T22:02:41Z
  received_date 2022-11-13T22:02:42Z
  format xarf2event
  Incident part
  url: https://www.logino365.click/
  domain: www.logino365.click
  phishing_url: https://www.logino365.click/
  phishing_domain: www.logino365.click
  on_behalf_of_complainant_organisation: Microsoft (Phishing)
  on_behalf_of_complainant_url: www.microsoft.com
  ```

- Screen shot of report:

  [![Image of copy of the abuse report](/images/2022-11-13-16-53-13.png)](/images/2022-11-13-16-53-13.png)

## Conclusion

Microsoft aggressively attempts to squash Phishing sites. I'm impressed with how quickly they reacted. Although, I'm a bit curious how they detected it so quickly. 

- Microsoft appears to use Netcraft services, which include Cybercrime (Phishing detection) services:

  [![Image of abuse report from netcraft](/images/2022-11-13-18-00-58.png)](/images/2022-11-13-18-00-58.png)

- Cisco Umbrella thinks it's potentially malicious:
  
  [![Image of umbrella domain reputation](/images/2022-11-13-17-40-31.png)](/images/2022-11-13-17-40-31.png)

- SSL certificates are logged in `Certificate Transparency Database` (https://crt.sh/?q=logino365.click), they may scrap this info looking for patterns:

  [![Image of certificate transparency database from crt.sh](/images/2022-11-13-17-54-40.png)](/images/2022-11-13-17-54-40.png)

- The Domains Project scanned the site pretty quickly:

  ```
  [21:57:54] [war] [o365] unauthorized request: https://login.logino365.click/ (Mozilla/5.0 (compatible; Domains Project/1.3.7; +https://domainsproject.org)) [165.227.197.137]
  ```

- Maybe Digital Ocean is watching DNS queries against a Intel List? A few days later OpenDNS is blocking the domain (phishing sites spread quickly):

  ```
  $ host logino365.click 208.67.222.222
  logino365.click has address 146.112.61.108

  $ host 146.112.61.108
  108.61.112.146.in-addr.arpa domain name pointer hit-phish.opendns.com.
  ```

- A few days later I've been added to the Google SafeBrowsing database:

  [![Image of screen shot of google safe browsing alert](/images/2022-11-19-12-24-21.png)](/images/2022-11-19-12-24-21.png)

I'm impressed by the quick anti-Phishing detection services. Good job to all.  

Overall, it was cheap and easy to setup.  However, if you create a Phishing site that is "too good" - Microsoft will catch it quickly. I see poorly crafted Office 365 Phishing sites that stay up for weeks no matter how many times you report it.