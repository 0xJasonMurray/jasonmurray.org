---
title: "Configure Client Wireguard VPN Server on Linux"
date: 2021-11-02T10:06:59-05:00
toc: false
images:
categories:
  - tech
tags: 
  - wireguard
  - vpn
  - linux
description: How to use Stanislas's configuration scripts to configure a WireGuard client VPN server on Linux.
---
How to use [Stanislas's](https://stanislas.blog/) configuration scripts to configure a WireGuard client VPN server on Linux.

## Configuration Details

### Linux Server Configuration

Install Wireguard configuration script:

```text
curl -O https://raw.githubusercontent.com/angristan/wireguard-install/master/wireguard-install.sh
```

Example Output:

```
jemurray@home-server:~$ curl -O https://raw.githubusercontent.com/angristan/wireguard-install/master/wireguard-install.sh
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100 15088  100 15088    0     0   8533      0  0:00:01  0:00:01 --:--:--  8529
```

Make the script executable:

```text
chmod +x wireguard-install.sh
```

Example Output:

```
jemurray@home-server:~$ chmod +x wireguard-install.sh
```

Execute the installation script:

```text
./wireguard-install.sh
```

Example output:

```
jemurray@home-server:~$ sudo ./wireguard-install.sh
Welcome to the WireGuard installer!
The git repository is available at: https://github.com/angristan/wireguard-install

I need to ask you a few questions before starting the setup.
You can leave the default options and just press enter if you are ok with them.

IPv4 or IPv6 public address: 99.28.30.123
Public interface: ens160
WireGuard interface name: wg0
Server's WireGuard IPv4: 10.66.66.1
Server's WireGuard IPv6: fd42:42:42::1
Server's WireGuard port [1-65535]: 49111
First DNS resolver to use for the clients: 208.67.222.222
Second DNS resolver to use for the clients (optional): 208.67.220.220

Okay, that was all I needed. We are ready to setup your WireGuard server now.
You will be able to generate a client at the end of the installation.
Press any key to continue...
Hit:1 http://us.archive.ubuntu.com/ubuntu bionic InRelease
Get:2 https://download.docker.com/linux/ubuntu bionic InRelease [64.4 kB]
Hit:3 https://packages.grafana.com/oss/deb stable InRelease
...
Tell me a name for the client.
The name must consist of alphanumeric character. It may also include an underscore or a dash and can't exceed 15 chars.
Client name: phalanges
Client's WireGuard IPv4: 10.66.66.2
Client's WireGuard IPv6: fd42:42:42::2

Here is your client config file as a QR Code:
....QR CODE PRINTED HERE...
It is also available in /home/jemurray/wg0-client-phalanges.conf
If you want to add more clients, you simply need to run this script another time!
```

### Client Configuration

Copy the configuration file `/home/jemurray/wg0-client-phalanges.conf` from the previous step to the client.


In this example, we are using the macOS `wireguard` client:

[![Image of ](/images/2021-11-02-21-57-46.png)](/images/2021-11-02-21-57-46.png)

Import the configuration file into the client:

[![Image of ](/images/2021-11-02-21-53-45.png)](/images/2021-11-02-21-53-45.png)

Enable the VPN by clicking the name of the VPN profile:

[![Image of ](/images/2021-11-02-21-59-15.png)](/images/2021-11-02-21-59-15.png)

Example of activated VPN:

[![Image of ](/images/2021-11-02-22-01-22.png)](/images/2021-11-02-22-01-22.png)

Validate VPN connection:

```
jemurray@phalanges:~ $ ping 192.168.86.5
PING 192.168.86.5 (192.168.86.5): 56 data bytes
64 bytes from 192.168.86.5: icmp_seq=0 ttl=64 time=83.039 ms
64 bytes from 192.168.86.5: icmp_seq=1 ttl=64 time=45.536 ms
^C
--- 192.168.86.5 ping statistics ---
2 packets transmitted, 2 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 45.536/64.287/83.039/18.752 ms
```

