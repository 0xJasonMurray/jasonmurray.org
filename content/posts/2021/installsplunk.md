---
title: "Install Splunk Enterprise in a Lab Environment"
date: 2021-12-21T21:26:05-06:00
toc: false
images:
categories:
  - tech
tags: 
  - splunk
description: A basic guide to installing Splunk Enterprise in a lab environment. 
---

A quick start guide to installing Splunk Enterprise on Ubuntu server for a test lab environment.

Download the latest version of Splunk Enterprise. At the time of this writing, the latest version is 8.2.4.  Check [here](https://www.splunk.com/en_us/download/splunk-enterprise.html) for the latest:

```text
wget -O splunk-8.2.4-87e2dda940d1-linux-2.6-amd64.deb 'https://download.splunk.com/products/splunk/releases/8.2.4/linux/splunk-8.2.4-87e2dda940d1-linux-2.6-amd64.deb'
```

Example output:

```
jemurray@splunk:~$ wget -O splunk-8.2.4-87e2dda940d1-linux-2.6-amd64.deb 'https://download.splunk.com/products/splunk/releases/8.2.4/linux/splunk-8.2.4-87e2dda940d1-linux-2.6-amd64.deb'
--2021-12-23 03:17:21--  https://download.splunk.com/products/splunk/releases/8.2.4/linux/splunk-8.2.4-87e2dda940d1-linux-2.6-amd64.deb
Resolving download.splunk.com (download.splunk.com)... 2600:9000:2073:e600:1d:f9c1:d100:93a1, 2600:9000:2073:6000:1d:f9c1:d100:93a1, 2600:9000:2073:f800:1d:f9c1:d100:93a1, ...
Connecting to download.splunk.com (download.splunk.com)|2600:9000:2073:e600:1d:f9c1:d100:93a1|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 434892008 (415M) [binary/octet-stream]
Saving to: ‘splunk-8.2.4-87e2dda940d1-linux-2.6-amd64.deb’

splunk-8.2.4-87e2dda940d1-linux-2.6-a 100%[========================================================================>] 414.75M  18.9MB/s    in 24s

2021-12-23 03:17:45 (17.1 MB/s) - ‘splunk-8.2.4-87e2dda940d1-linux-2.6-amd64.deb’ saved [434892008/434892008]
```

Install the .deb package:

```text
sudo dpkg -i splunk-8.2.4-87e2dda940d1-linux-2.6-amd64.deb
```

Example output:

```
jemurray@splunk:~$ sudo dpkg -i splunk-8.2.4-87e2dda940d1-linux-2.6-amd64.deb
(Reading database ... 170449 files and directories currently installed.)
Preparing to unpack splunk-8.2.4-87e2dda940d1-linux-2.6-amd64.deb ...
Unpacking splunk (8.2.4) over (8.2.2.1) ...
Setting up splunk (8.2.4) ...
complete
```
Start Splunk and agree to the license:

```text
sudo ./splunk start
```

Example output:

```
jemurray@splunk:/opt/splunk/bin$ sudo ./splunk start
SPLUNK GENERAL TERMS

Last Updated: August 12, 2021

These Splunk General Terms ("General Terms") between Splunk Inc., a Delaware
corporation, with its principal place of business at 270 Brannan Street, San
Francisco, California 94107, U.S.A ("Splunk" or "we" or "us" or "our") and you
("Customer" or "you" or "your") apply to the purchase of licenses and
subscriptions for Splunk's Offerings. By clicking on the appropriate button,
or by downloading, installing, accessing or using the Offerings, you agree to
these General Terms. If you are entering into these General Terms on behalf of
Customer, you represent that you have the authority to bind Customer. If you
do not agree to these General Terms, or if you are not authorized to accept
the General Terms on behalf of the Customer, do not download, install, access,
or use any of the Offerings.

See the General Terms Definitions Exhibit attached for definitions of
capitalized terms not defined herein.

1. License Rights
(A) General Rights. You have the nonexclusive, worldwide, nontransferable and
nonsublicensable right, subject to payment of applicable Fees and compliance
with the terms of these General Terms, to use your Purchased Offerings for
your Internal Business Purposes during the Term and up to the Capacity
purchased.

(B) Copies for On-Premises Products. You have the right to make a reasonable
number of copies of On-Premises Products for archival and back-up purposes.

(C) Splunk Extensions. You may use Splunk Extensions solely in connection with
the applicable Purchased Offering subject to the same terms and conditions for
that Offering (including with respect to Term) and payment of any Fees
associated with the Splunk Extensions. Some Splunk Extensions may be made
available under license terms that provide broader rights than the license
rights you have to the applicable underlying Offering (e.g., if the Extension
is Open Source Software). These broader rights will apply to that Splunk
Extension. Splunk Extensions may be installed on Hosted Services pursuant to
Do you agree with this license? [y/n]: y

This appears to be your first time running this version of Splunk.

Splunk software must create an administrator account during startup. Otherwise, you cannot log in.
Create credentials for the administrator account.
Characters do not appear on the screen when you type in credentials.

Please enter an administrator username: jemurray
Password must contain at least:
   * 8 total printable ASCII character(s).
Please enter a new password:
Please confirm new password:
Copying '/opt/splunk/etc/openldap/ldap.conf.default' to '/opt/splunk/etc/openldap/ldap.conf'.
Generating RSA private key, 2048 bit long modulus
..................................................+++++
............................+++++
e is 65537 (0x10001)
writing RSA key

Generating RSA private key, 2048 bit long modulus
...........+++++
.....+++++
e is 65537 (0x10001)
writing RSA key

Moving '/opt/splunk/share/splunk/search_mrsparkle/modules.new' to '/opt/splunk/share/splunk/search_mrsparkle/modules'.

Splunk> Take the sh out of IT.

Checking prerequisites...
	Checking http port [8000]: open
	Checking mgmt port [8089]: open
	Checking appserver port [127.0.0.1:8065]: open
	Checking kvstore port [8191]: open
	Checking configuration... Done.
		Creating: /opt/splunk/var/lib/splunk
		Creating: /opt/splunk/var/run/splunk
		Creating: /opt/splunk/var/run/splunk/appserver/i18n
		Creating: /opt/splunk/var/run/splunk/appserver/modules/static/css
		Creating: /opt/splunk/var/run/splunk/upload
		Creating: /opt/splunk/var/run/splunk/search_telemetry
		Creating: /opt/splunk/var/spool/splunk
		Creating: /opt/splunk/var/spool/dirmoncache
		Creating: /opt/splunk/var/lib/splunk/authDb
		Creating: /opt/splunk/var/lib/splunk/hashDb
New certs have been generated in '/opt/splunk/etc/auth'.
	Checking critical directories...	Done
	Checking indexes...
		Validated: _audit _internal _introspection _metrics _metrics_rollup _telemetry _thefishbucket history main summary
	Done
	Checking filesystem compatibility...  Done
	Checking conf files for problems...
	Done
	Checking default conf files for edits...
	Validating installed files against hashes from '/opt/splunk/splunk-8.2.4-87e2dda940d1-linux-2.6-x86_64-manifest'
	All installed files intact.
	Done
All preliminary checks passed.

Starting splunk server daemon (splunkd)...
Generating a RSA private key
..+++++
..........................................................................................+++++
writing new private key to 'privKeySecure.pem'
-----
Signature ok
subject=/CN=splunk/O=SplunkUser
Getting CA Private Key
writing RSA key
Done


Waiting for web server at http://127.0.0.1:8000 to be available............... Done


If you get stuck, we're here to help.
Look for answers here: http://docs.splunk.com

The Splunk web interface is at http://splunk:8000
```

Enable Splunk to receive remote log messages from the [Universal Forwarders](https://www.splunk.com/en_us/download/universal-forwarder.html):

```text
sudo ./splunk enable listen 9997 -auth jemurray:mypassword
```

That's it! Login to the web interface:

```text
http://splunk.local:8000/
```

[![Image of ](/images/2021-12-23-13-22-16.png)](/images/2021-12-23-13-22-16.png)

