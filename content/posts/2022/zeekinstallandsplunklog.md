---
title: "Zeek Installation and Splunk CIM Data Normalization Guide"
date: 2022-01-16T10:08:15-06:00
toc: false
images:
categories:
  - tech
tags: 
  - zeek
  - splunk
  - cim
  - datamodels
description: Log files become significantly more helpful when they are properly structured and share common data models with other systems. In this post, I will go over how to send logs from Zeek into Splunk using a CIM compliant data model.
---
Log files become significantly more helpful when they are properly structured and share common data models with other systems. In this post, I will go over how to send logs from Zeek into Splunk using a CIM compliant data model.

But first, it's important to understand what a CIM compliant data model is. According to [Splunk](https://docs.splunk.com/Documentation/CIM/5.0.0/User/Overview):

> The Splunk Common Information Model (CIM) is a shared semantic model focused on extracting value from data. The CIM is implemented as an add-on that contains a collection of data models, documentation, and tools that support the consistent, normalized treatment of data for maximum efficiency at search time.
>
> The CIM add-on contains a collection of preconfigured data models that you can apply to your data at search time. Each data model in the CIM consists of a set of field names and tags that define the least common denominator of a domain of interest. You can use these data models to normalize and validate data at search time, accelerate key data in searches and dashboards, or create new reports and visualizations with Pivot. 

Said another way, there are many different sources of data. Depending on the developer, the field names may differ.  For example, the `key:value` pair of an IP address could be: `ip:value` or `src:value` or `src_ip:value` or `ipaddress:value` or `sourceIP:value` or `id.orig_h:value` or any other combination.  The problem becomes obvious, how can we easily correlate data when the keys are all different?

In Splunk the CIM data model specifies how the [Source IP Address](https://docs.splunk.com/Documentation/CIM/latest/User/NetworkTraffic) should be labeled:

```
root@splunk:/opt/splunk/etc/apps/Splunk_SA_CIM/default/data/models# grep src_ip Network_Traffic.json
          "fieldName": "src_ip",
          "displayName": "src_ip",
```

When input data does not conform to the standard data model, Splunk uses a variety of different methods, such as `alias`, to map the data to the correct field name. 

```
root@splunk:/opt/splunk/etc/apps/Splunk_TA_zeek/default# grep src_ip0 props.conf
FIELDALIAS-src_ip0 = id.orig_h AS src_ip
```


## Steps Overview

By the end of this post, you will have a fully installed and production ready Zeek instance sending properly formatted logs into Splunk.

The [UniversalForwarder](https://www.splunk.com/en_us/download/universal-forwarder.html) and [Zeek TA](https://splunkbase.splunk.com/app/5466/) are where the "magic" happens. Skip to these sections if your just here to normalize JSON Zeek data.

 - Install and configure Linux (Ubuntu 20.04.03 LTS)
 - Install and configure Zeek (latest feature release) using pre-built packages
 - Install and configure the Splunk Universal Forwarder
 - Install Zeek TA and configure Splunk (installing Splunk is outside the scope of this post)
 - Validate Zeek data is CIM compliant

## Install and Configure Ubuntu 20.04.03 LTS

Install Ubuntu 20.04.03 LTS per [this installation guide](https://ubuntu.com/tutorials/install-ubuntu-server): https://ubuntu.com/tutorials/install-ubuntu-server

Edit `/etc/netplan/00-installer-config.yaml` and add the following lines to set static IP addresses:

```
network:
  ethernets:
    eno1:
      addresses:
      - 192.168.86.241/24
      gateway4: 192.168.86.1
      nameservers:
        addresses:
        - 192.168.86.1
        - 1.1.1.1
    eno2:
      addresses:
      - 127.0.0.100/32
```

Apply the network changes:

```text
sudo netplan apply
```

Set the local timezone to CST:

```text
sudo timedatectl set-timezone America/Chicago
```

Enable the firewall:

```text
sudo ufw allow openssh
sudo ufw enable
```

## Install Zeek

Follow the directions from the [binary packages guide](https://software.opensuse.org//download.html?project=security%3Azeek&package=zeek), summarized here:

```text
echo 'deb http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04/ /' | sudo tee /etc/apt/sources.list.d/security:zeek.list
curl -fsSL https://download.opensuse.org/repositories/security:zeek/xUbuntu_20.04/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/security_zeek.gpg > /dev/null
sudo apt update
sudo apt install zeek zkg
```

Example output:

```
jemurray@zeek-01:~$ echo 'deb http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04/ /' | sudo tee /etc/apt/sources.list.d/security:zeek.list
[sudo] password for jemurray:
deb http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04/ /
jemurray@zeek-01:~$ curl -fsSL https://download.opensuse.org/repositories/security:zeek/xUbuntu_20.04/Release.key | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/security_zeek.gpg > /dev/null
jemurray@zeek-01:~$ sudo apt update
Hit:1 http://us.archive.ubuntu.com/ubuntu focal InRelease
0% [Working]
Get:2 http://us.archive.ubuntu.com/ubuntu focal-updates InRelease [114 kB]
Get:3 http://us.archive.ubuntu.com/ubuntu focal-backports InRelease [108 kB]
Get:4 http://us.archive.ubuntu.com/ubuntu focal-security InRelease [114 kB]
Get:5 http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04  InRelease [1,563 B]
Get:6 http://us.archive.ubuntu.com/ubuntu focal-security/universe amd64 Packages [675 kB]
Get:7 http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04  Packages [12.6 kB]
Fetched 1,025 kB in 1s (836 kB/s)
Reading package lists... Done
Building dependency tree
Reading state information... Done
45 packages can be upgraded. Run 'apt list --upgradable' to see them.
jemurray@zeek-01:~$
jemurray@zeek-01:~$ sudo apt install zeek
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  libbroker-dev libc-dev-bin libc6-dev libcrypt-dev libmaxminddb-dev libpcap-dev libpcap0.8-dev libssl-dev libssl1.1 linux-libc-dev manpages-dev postfix python3-git python3-gitdb
  python3-semantic-version python3-smmap ssl-cert zeek-btest zeek-btest-data zeek-core zeek-core-dev zeek-libcaf-dev zeek-zkg zeekctl zlib1g-dev
Suggested packages:
  glibc-doc libssl-doc procmail postfix-mysql postfix-pgsql postfix-ldap postfix-pcre postfix-lmdb postfix-sqlite sasl2-bin | dovecot-common resolvconf postfix-cdb mail-reader
  postfix-doc python-git-doc python-semantic-version-doc python3-nose openssl-blacklist
The following NEW packages will be installed:
  libbroker-dev libc-dev-bin libc6-dev libcrypt-dev libmaxminddb-dev libpcap-dev libpcap0.8-dev libssl-dev linux-libc-dev manpages-dev postfix python3-git python3-gitdb
  python3-semantic-version python3-smmap ssl-cert zeek zeek-btest zeek-btest-data zeek-core zeek-core-dev zeek-libcaf-dev zeek-zkg zeekctl zlib1g-dev
The following packages will be upgraded:
  libssl1.1
1 upgraded, 25 newly installed, 0 to remove and 44 not upgraded.
Need to get 31.8 MB of archives.
After this operation, 129 MB of additional disk space will be used.
Do you want to continue? [Y/n] y
Get:1 http://us.archive.ubuntu.com/ubuntu focal-updates/main amd64 libssl1.1 amd64 1.1.1f-1ubuntu2.10 [1,322 kB]
Get:2 http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04  libbroker-dev 4.1.1-0 [1,256 kB]
Get:3 http://us.archive.ubuntu.com/ubuntu focal-updates/main amd64 libc-dev-bin amd64 2.31-0ubuntu9.2 [71.8 kB]
Get:4 http://us.archive.ubuntu.com/ubuntu focal-updates/main amd64 linux-libc-dev amd64 5.4.0-94.106 [1,114 kB]
Get:5 http://us.archive.ubuntu.com/ubuntu focal/main amd64 libcrypt-dev amd64 1:4.4.10-10ubuntu4 [104 kB]
Get:6 http://us.archive.ubuntu.com/ubuntu focal-updates/main amd64 libc6-dev amd64 2.31-0ubuntu9.2 [2,520 kB]
Get:7 http://us.archive.ubuntu.com/ubuntu focal-updates/main amd64 libmaxminddb-dev amd64 1.4.2-0ubuntu1.20.04.1 [15.4 kB]
Get:8 http://us.archive.ubuntu.com/ubuntu focal/main amd64 libpcap0.8-dev amd64 1.9.1-3 [244 kB]
Get:9 http://us.archive.ubuntu.com/ubuntu focal/main amd64 libpcap-dev amd64 1.9.1-3 [3,484 B]
Get:10 http://us.archive.ubuntu.com/ubuntu focal-updates/main amd64 libssl-dev amd64 1.1.1f-1ubuntu2.10 [1,584 kB]
Get:11 http://us.archive.ubuntu.com/ubuntu focal/main amd64 manpages-dev all 5.05-1 [2,266 kB]
Get:12 http://us.archive.ubuntu.com/ubuntu focal/main amd64 ssl-cert all 1.0.39 [17.0 kB]
Get:13 http://us.archive.ubuntu.com/ubuntu focal-updates/main amd64 postfix amd64 3.4.13-0ubuntu1.2 [1,201 kB]
Get:14 http://us.archive.ubuntu.com/ubuntu focal/universe amd64 python3-smmap all 2.0.5-2 [21.2 kB]
Get:15 http://us.archive.ubuntu.com/ubuntu focal/universe amd64 python3-gitdb all 2.0.6-2 [46.6 kB]
Get:16 http://us.archive.ubuntu.com/ubuntu focal/universe amd64 python3-git all 3.0.7-1 [302 kB]
Get:17 http://us.archive.ubuntu.com/ubuntu focal/main amd64 python3-semantic-version all 2.8.2-2 [14.7 kB]
Get:18 http://us.archive.ubuntu.com/ubuntu focal-updates/main amd64 zlib1g-dev amd64 1:1.2.11.dfsg-2ubuntu1.2 [155 kB]
Get:19 http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04  zeek-core 4.1.1-0 [4,946 kB]
Get:20 http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04  zeekctl 4.1.1-0 [1,921 kB]
Get:21 http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04  zeek-libcaf-dev 4.1.1-0 [1,315 kB]
Get:22 http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04  zeek-core-dev 4.1.1-0 [855 kB]
Get:23 http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04  zeek-btest 4.1.1-0 [30.5 kB]
Get:24 http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04  zeek-btest-data 4.1.1-0 [10.4 MB]
Get:25 http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04  zeek-zkg 4.1.1-0 [52.2 kB]
Get:26 http://download.opensuse.org/repositories/security:/zeek/xUbuntu_20.04  zeek 4.1.1-0 [1,104 B]
Fetched 31.8 MB in 3s (12.7 MB/s)
Preconfiguring packages ...
(Reading database ... 71558 files and directories currently installed.)
Preparing to unpack .../00-libssl1.1_1.1.1f-1ubuntu2.10_amd64.deb ...
Unpacking libssl1.1:amd64 (1.1.1f-1ubuntu2.10) over (1.1.1f-1ubuntu2.8) ...
Selecting previously unselected package libbroker-dev.
Preparing to unpack .../01-libbroker-dev_4.1.1-0_amd64.deb ...
Unpacking libbroker-dev (4.1.1-0) ...
Selecting previously unselected package libc-dev-bin.
Preparing to unpack .../02-libc-dev-bin_2.31-0ubuntu9.2_amd64.deb ...
Unpacking libc-dev-bin (2.31-0ubuntu9.2) ...
Selecting previously unselected package linux-libc-dev:amd64.
Preparing to unpack .../03-linux-libc-dev_5.4.0-94.106_amd64.deb ...
Unpacking linux-libc-dev:amd64 (5.4.0-94.106) ...
Selecting previously unselected package libcrypt-dev:amd64.
Preparing to unpack .../04-libcrypt-dev_1%3a4.4.10-10ubuntu4_amd64.deb ...
Unpacking libcrypt-dev:amd64 (1:4.4.10-10ubuntu4) ...
Selecting previously unselected package libc6-dev:amd64.
Preparing to unpack .../05-libc6-dev_2.31-0ubuntu9.2_amd64.deb ...
Unpacking libc6-dev:amd64 (2.31-0ubuntu9.2) ...
Selecting previously unselected package libmaxminddb-dev:amd64.
Preparing to unpack .../06-libmaxminddb-dev_1.4.2-0ubuntu1.20.04.1_amd64.deb ...
Unpacking libmaxminddb-dev:amd64 (1.4.2-0ubuntu1.20.04.1) ...
Selecting previously unselected package libpcap0.8-dev:amd64.
Preparing to unpack .../07-libpcap0.8-dev_1.9.1-3_amd64.deb ...
Unpacking libpcap0.8-dev:amd64 (1.9.1-3) ...
Selecting previously unselected package libpcap-dev:amd64.
Preparing to unpack .../08-libpcap-dev_1.9.1-3_amd64.deb ...
Unpacking libpcap-dev:amd64 (1.9.1-3) ...
Selecting previously unselected package libssl-dev:amd64.
Preparing to unpack .../09-libssl-dev_1.1.1f-1ubuntu2.10_amd64.deb ...
Unpacking libssl-dev:amd64 (1.1.1f-1ubuntu2.10) ...
Selecting previously unselected package manpages-dev.
Preparing to unpack .../10-manpages-dev_5.05-1_all.deb ...
Unpacking manpages-dev (5.05-1) ...
Selecting previously unselected package ssl-cert.
Preparing to unpack .../11-ssl-cert_1.0.39_all.deb ...
Unpacking ssl-cert (1.0.39) ...
Selecting previously unselected package postfix.
Preparing to unpack .../12-postfix_3.4.13-0ubuntu1.2_amd64.deb ...
Unpacking postfix (3.4.13-0ubuntu1.2) ...
Selecting previously unselected package python3-smmap.
Preparing to unpack .../13-python3-smmap_2.0.5-2_all.deb ...
Unpacking python3-smmap (2.0.5-2) ...
Selecting previously unselected package python3-gitdb.
Preparing to unpack .../14-python3-gitdb_2.0.6-2_all.deb ...
Unpacking python3-gitdb (2.0.6-2) ...
Selecting previously unselected package python3-git.
Preparing to unpack .../15-python3-git_3.0.7-1_all.deb ...
Unpacking python3-git (3.0.7-1) ...
Selecting previously unselected package python3-semantic-version.
Preparing to unpack .../16-python3-semantic-version_2.8.2-2_all.deb ...
Unpacking python3-semantic-version (2.8.2-2) ...
Selecting previously unselected package zeek-core.
Preparing to unpack .../17-zeek-core_4.1.1-0_amd64.deb ...
Unpacking zeek-core (4.1.1-0) ...
Selecting previously unselected package zeekctl.
Preparing to unpack .../18-zeekctl_4.1.1-0_amd64.deb ...
Unpacking zeekctl (4.1.1-0) ...
Selecting previously unselected package zeek-libcaf-dev.
Preparing to unpack .../19-zeek-libcaf-dev_4.1.1-0_amd64.deb ...
Unpacking zeek-libcaf-dev (4.1.1-0) ...
Selecting previously unselected package zlib1g-dev:amd64.
Preparing to unpack .../20-zlib1g-dev_1%3a1.2.11.dfsg-2ubuntu1.2_amd64.deb ...
Unpacking zlib1g-dev:amd64 (1:1.2.11.dfsg-2ubuntu1.2) ...
Selecting previously unselected package zeek-core-dev.
Preparing to unpack .../21-zeek-core-dev_4.1.1-0_amd64.deb ...
Unpacking zeek-core-dev (4.1.1-0) ...
Selecting previously unselected package zeek-btest.
Preparing to unpack .../22-zeek-btest_4.1.1-0_amd64.deb ...
Unpacking zeek-btest (4.1.1-0) ...
Selecting previously unselected package zeek-btest-data.
Preparing to unpack .../23-zeek-btest-data_4.1.1-0_amd64.deb ...
Unpacking zeek-btest-data (4.1.1-0) ...
Selecting previously unselected package zeek-zkg.
Preparing to unpack .../24-zeek-zkg_4.1.1-0_amd64.deb ...
Unpacking zeek-zkg (4.1.1-0) ...
Selecting previously unselected package zeek.
Preparing to unpack .../25-zeek_4.1.1-0_amd64.deb ...
Unpacking zeek (4.1.1-0) ...
Setting up manpages-dev (5.05-1) ...
Setting up libssl1.1:amd64 (1.1.1f-1ubuntu2.10) ...
Setting up libmaxminddb-dev:amd64 (1.4.2-0ubuntu1.20.04.1) ...
Setting up linux-libc-dev:amd64 (5.4.0-94.106) ...
Setting up zeek-btest (4.1.1-0) ...
Setting up ssl-cert (1.0.39) ...
Setting up libssl-dev:amd64 (1.1.1f-1ubuntu2.10) ...
Setting up python3-semantic-version (2.8.2-2) ...
Setting up postfix (3.4.13-0ubuntu1.2) ...
Adding group `postfix' (GID 118) ...
Done.
Adding system user `postfix' (UID 113) ...
Adding new user `postfix' (UID 113) with group `postfix' ...
Not creating home directory `/var/spool/postfix'.
Creating /etc/postfix/dynamicmaps.cf
Adding group `postdrop' (GID 119) ...
Done.
setting myhostname: zeek-01.lan
setting alias maps
setting alias database
changing /etc/mailname to zeek-01.local
setting myorigin
setting destinations: $myhostname, zeek-01.local, zeek-01, localhost.localdomain, localhost
setting relayhost:
setting mynetworks: 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
setting mailbox_size_limit: 0
setting recipient_delimiter: +
setting inet_interfaces: all
setting inet_protocols: all
/etc/aliases does not exist, creating it.
WARNING: /etc/aliases exists, but does not have a root alias.

Postfix (main.cf) is now set up with a default configuration.  If you need to
make changes, edit /etc/postfix/main.cf (and others) as needed.  To view
Postfix configuration values, see postconf(1).

After modifying main.cf, be sure to run 'systemctl reload postfix'.

Running newaliases
Created symlink /etc/systemd/system/multi-user.target.wants/postfix.service → /lib/systemd/system/postfix.service.
Setting up libbroker-dev (4.1.1-0) ...
Setting up libcrypt-dev:amd64 (1:4.4.10-10ubuntu4) ...
Setting up zeek-libcaf-dev (4.1.1-0) ...
Setting up libc-dev-bin (2.31-0ubuntu9.2) ...
Setting up python3-smmap (2.0.5-2) ...
Setting up zeek-core (4.1.1-0) ...
Setting up zeek-btest-data (4.1.1-0) ...
Setting up python3-gitdb (2.0.6-2) ...
Setting up python3-git (3.0.7-1) ...
Setting up zeekctl (4.1.1-0) ...
Setting up libc6-dev:amd64 (2.31-0ubuntu9.2) ...
Setting up zeek-zkg (4.1.1-0) ...
Setting up libpcap0.8-dev:amd64 (1.9.1-3) ...
Setting up zlib1g-dev:amd64 (1:1.2.11.dfsg-2ubuntu1.2) ...
Setting up libpcap-dev:amd64 (1.9.1-3) ...
Setting up zeek-core-dev (4.1.1-0) ...
Setting up zeek (4.1.1-0) ...
Processing triggers for rsyslog (8.2001.0-1ubuntu1.1) ...
Processing triggers for ufw (0.36-6) ...
Processing triggers for systemd (245.4-4ubuntu3.11) ...
Processing triggers for man-db (2.9.1-1) ...
Processing triggers for libc-bin (2.31-0ubuntu9.2) ...
```

## Configure Zeek

Add Zeek `/opt/zeek/bin` to user `$PATH`:

```text
echo "PATH=$PATH:/opt/zeek/bin" >> ~/.bashrc
source ~/.bashrc
```

Edit `/opt/zeek/etc/node.cfg` with the following:

```text
[zeek]
type=standalone
host=localhost
interface=eno2  # Change this to the interface connected to the network TAP port
```

Edit `/opt/zeek/share/zeek/site/local.zeek` to enable the `json` logging format:

```text
# Output to JSON
@load policy/tuning/json-logs.zeek
```

Install Zeek Configs:

```text
sudo /opt/zeek/bin/zeekctl install
```

Example output:

```
jemurray@zeek-01:/opt/zeek/etc$ sudo /opt/zeek/bin/zeekctl install
creating policy directories ...
installing site policies ...
generating standalone-layout.zeek ...
generating local-networks.zeek ...
generating zeekctl-config.zeek ...
generating zeekctl-config.sh ...
```

Start Zeek:

```text
sudo /opt/zeek/bin/zeekctl start
```

Example Output:

```
jemurray@zeek-01:~$ sudo /opt/zeek/bin/zeekctl start
starting zeek ...

jemurray@zeek-01:~$ sudo /opt/zeek/bin/zeekctl status
Name         Type       Host          Status    Pid    Started
zeek         standalone localhost     running   11237  11 Jan 21:31:45
```

Configure Zeek to start automatically on boot by creating the file `/etc/systemd/system/zeek.service` with the following contents:


```text
[Unit]
Description=Zeek Collection Server
After=network.target

[Service]
[Unit]
Description=Zeek Collection Server
After=network.target

[Service]
ExecStartPre=/opt/zeek/bin/zeekctl check
ExecStart=/opt/zeek/bin/zeekctl start
ExecReload=/opt/zeek/bin/zeekctl reload
ExecStop=/opt/zeek/bin/zeekctl stop
Restart=on-failure
RestartPreventExitStatus=255
Type=forking

[Install]
WantedBy=multi-user.target
Alias=zeek.service
```

Start the service:

```text
sudo systemctl start zeek
```

Create a `root` cron job which runs every 5 minutes to check the status of Zeek, restart if needed, remove expired log files, and other general house keeping:

```text
sudo crontab -e
# Add the following line
*/5 * * * * /opt/zeek/bin/zeekctl cron
```

## Install Splunk Universal Forwarder

```text
wget -O splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-amd64.deb 'https://download.splunk.com/products/universalforwarder/releases/8.2.4/linux/splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-amd64.deb'
```

Example Output:

```
jemurray@zeek-01:~$ wget -O splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-amd64.deb 'https://download.splunk.com/products/universalforwarder/releases/8.2.4/linux/splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-amd64.deb'
--2022-01-11 21:40:23--  https://download.splunk.com/products/universalforwarder/releases/8.2.4/linux/splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-amd64.deb
Resolving download.splunk.com (download.splunk.com)... 2600:9000:2073:e00:1d:f9c1:d100:93a1, 2600:9000:2073:f000:1d:f9c1:d100:93a1, 2600:9000:2073:b400:1d:f9c1:d100:93a1, ...
Connecting to download.splunk.com (download.splunk.com)|2600:9000:2073:e00:1d:f9c1:d100:93a1|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 25948752 (25M) [binary/octet-stream]
Saving to: ‘splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-amd64.deb’

splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-a 100%[=================================================================================================>]  24.75M  55.3MB/s    in 0.4s

2022-01-11 21:40:24 (55.3 MB/s) - ‘splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-amd64.deb’ saved [25948752/25948752]
```

Install the `dpkg`:

```text
sudo dpkg -i ./splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-amd64.deb
```

Example Output:

```
jemurray@zeek-01:~$ sudo dpkg -i ./splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-amd64.deb
Selecting previously unselected package splunkforwarder.
(Reading database ... 78852 files and directories currently installed.)
Preparing to unpack .../splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-amd64.deb ...
Unpacking splunkforwarder (8.2.4) ...
Setting up splunkforwarder (8.2.4) ...
complete
```

## Configure Splunk Universal Forwarder

Send logs to the Splunk server (replace IP:PORT with the IP and port of your Splunk server):

```text
sudo /opt/splunkforwarder/bin/splunk add forward-server 192.168.86.47:9997
```

Example Output:

```
jemurray@zeek-01:~$ sudo /opt/splunkforwarder/bin/splunk add forward-server 192.168.86.47:9997
SPLUNK GENERAL TERMS

Last Updated: August 12, 2021

These Splunk General Terms ("General Terms") between Splunk Inc., a Delaware
....blah blah blah licenses....
Added forwarding to: 192.168.86.47:9997.
```

Add the following lines to the `/opt/splunkforwarder/etc/system/local/inputs.conf` file.

Now is a good time to pause and explain the `source` and `sourcetype` fields in this file. This is the "magic" I talked about earlier.  The Zeek TA requires the `source` and `sourcetype` fields to match a specific format otherwise the `eventtypes.conf`, `props.conf`, `transforms.conf`, etc. won't match and process the data normalization functions within Splunk. 

Remember what we said earlier, each developer can specify their own logging and naming formats. This is no exception, the Splunk TA for Zeek does not match the default logging and naming formats of a modern Zeek installation.

There are many different sections in the TA where Splunk is looking for `bro.*.log` or `bro:*:json`. By default, Zeek creates log files using the naming convention `conn.log` or `ssl.log`. If the source is not set, the Splunk TA will not process the data.

Important Note: The `sourcetype` is the same for all log files, a Splunk `transform` will set it properly as logs are ingested. For high volume systems, the `transforms.conf` should be adjusted and and the `sourcetype` in the `inputs.conf` manually set to avoid performance issues.

```text
[default]
host = zeek-01

[monitor:///opt/zeek/logs/current/conn.log]
_TCP_ROUTING = *
index = zeek
source = bro.conn.log
sourcetype = bro:json

[monitor:///opt/zeek/logs/current/dns.log]
_TCP_ROUTING = *
index = zeek
source = bro.dns.log
sourcetype = bro:json

[monitor:///opt/zeek/logs/current/software.log]
_TCP_ROUTING = *
index = zeek
source = bro.software.log
sourcetype = bro:json

[monitor:///opt/zeek/logs/current/smtp.log]
_TCP_ROUTING = *
index = zeek
source = bro.smtp.log
sourcetype = bro:json

[monitor:///opt/zeek/logs/current/ssl.log]
_TCP_ROUTING = *
index = zeek
source = bro.ssl.log
sourcetype = bro:json

[monitor:///opt/zeek/logs/current/ssh.log]
_TCP_ROUTING = *
index = zeek
source = bro.ssh.log
sourcetype = bro:json

[monitor:///opt/zeek/logs/current/x509.log]
_TCP_ROUTING = *
index = zeek
source = bro.x509.log
sourcetype = bro:json

[monitor:///opt/zeek/logs/current/ftp.log]
_TCP_ROUTING = *
index = zeek
source = bro.ftp.log
sourcetype = bro:json

[monitor:///opt/zeek/logs/current/http.log]
_TCP_ROUTING = *
index = zeek
source = bro.http.log
sourcetype = bro:json

[monitor:///opt/zeek/logs/current/rdp.log]
_TCP_ROUTING = *
index = zeek
source = bro.rdp.log
sourcetype = bro:json

[monitor:///opt/zeek/logs/current/smb_files.log]
_TCP_ROUTING = *
index = zeek
source = bro.smb_files.log
sourcetype = bro:json

[monitor:///opt/zeek/logs/current/smb_mapping.log]
_TCP_ROUTING = *
index = zeek
source = bro.smb_mapping.log
sourcetype = bro:json

[monitor:///opt/zeek/logs/current/snmp.log]
_TCP_ROUTING = *
index = zeek
source = bro.snmp.log
sourcetype = bro:json

[monitor:///opt/zeek/logs/current/sip.log]
_TCP_ROUTING = *
index = zeek
source = bro.sip.log
sourcetype = bro:json

[monitor:///opt/zeek/logs/current/files.log]
_TCP_ROUTING = *
index = zeek
source = bro.files.log
sourcetype = bro:json
```

Start the Splunk Universal Forwarder:

```text
sudo /opt/splunkforwarder/bin/splunk start
```


Example Output:

```
root@zeek-01:/opt/splunkforwarder/etc/system/local# sudo /opt/splunkforwarder/bin/splunk start

Splunk> Like an F-18, bro.

Checking prerequisites...
	Checking mgmt port [8089]: open
		Creating: /opt/splunkforwarder/var/run/splunk/appserver/i18n
		Creating: /opt/splunkforwarder/var/run/splunk/appserver/modules/static/css
		Creating: /opt/splunkforwarder/var/run/splunk/upload
		Creating: /opt/splunkforwarder/var/run/splunk/search_telemetry
		Creating: /opt/splunkforwarder/var/spool/splunk
		Creating: /opt/splunkforwarder/var/spool/dirmoncache
		Creating: /opt/splunkforwarder/var/lib/splunk/authDb
		Creating: /opt/splunkforwarder/var/lib/splunk/hashDb
New certs have been generated in '/opt/splunkforwarder/etc/auth'.
	Checking conf files for problems...
	Done
	Checking default conf files for edits...
	Validating installed files against hashes from '/opt/splunkforwarder/splunkforwarder-8.2.4-87e2dda940d1-linux-2.6-x86_64-manifest'
	All installed files intact.
	Done
All preliminary checks passed.

Starting splunk server daemon (splunkd)...
Done
```

Enable Splunk to start on boot:

```text
sudo /opt/splunkforwarder/bin/splunk enable boot-start
```

Example Output:

```
jemurray@zeek-01:~$ sudo /opt/splunkforwarder/bin/splunk enable boot-start
Init script installed at /etc/init.d/splunk.
Init script is configured to run at boot.
```

## Configure Splunk

Create a `zeek` index in Splunk (`Settings -> Indexes`):

[![Image of creating a zeek index in Splunk](/images/2022-01-11-16-09-06.png)](/images/2022-01-11-16-09-06.png)

Install the Splunk TA for Zeek by downloading and installing this package: https://splunkbase.splunk.com/app/5466/

In Splunk go to `Apps -> Find More Apps` and search for `TA for Zeek`, click the Install Button:

[![Image of TA for Zeek APP Installation Screen](/images/2022-01-11-16-02-36.png)](/images/2022-01-11-16-02-36.png)

Create a listening port for the UniversalForwarder to connect to (Settings -> Forwarding and receiving -> New Receiving Port):

[![Image of setup new listening port](/images/2022-01-16-12-14-09.png)](/images/2022-01-16-12-14-09.png)

## Validate Data is Flowing to Splunk

Validate the source and sourcetype fields are properly logging:

[![Image of validating source and sourcetype in Splunk](/images/2022-01-11-16-12-43.png)](/images/2022-01-11-16-12-43.png)

Look at some data in Splunk. It should look similar to this with the fields properly parsed:

[![Image of example DNS logs](/images/2022-01-11-16-14-47.png)](/images/2022-01-11-16-14-47.png)

## Validating CIM Compliant Data

In Splunk go to `Apps -> Find More Apps` and search for `Common Information Model`, click the Install Button:

[![Image of Splunk Common Information Model APP Install ](/images/2022-01-11-16-17-21.png)](/images/2022-01-11-16-17-21.png)


Validate CIM data model tags and field aliases are working by running the following search:

```text
index=zeek host=zeek-01 (`cim_Network_Resolution_indexes`) tag=network tag=resolution tag=dns
```

[![Image of validate CIM tags](/images/2022-01-11-16-24-39.png)](/images/2022-01-11-16-24-39.png)

## Summary

Normalizing data is hard, in fact, I suggest it could be a full time job in many organizations. It took me quite a while looking through the Zeek TA and UniversalForwarder configuration files to understand how to "massage" the data to get the CIM post-processing working properly.

Even if [Splunkbase](https://splunkbase.splunk.com/) says the APP is CIM compliant: 

[![Image of MS app for Splunk with CIM compliance version](/images/2022-01-16-10-55-58.png)](/images/2022-01-16-10-55-58.png)

You many need to modify quite a few files to get everything working properly.

Happy Hunting!