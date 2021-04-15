---
title: "Configure an OpenGear serial port from the CLI"
date: 2021-04-15T09:24:30-05:00
toc: false
images:
categories:
  - tech
tags: 
  - opengear
  - serialconsole
description: How to make configuration changes to an Opengear serial port from the CLI.
---

`ssh` into the serial console:

```
jemurray@bastion-host:~$ ssh opengear-console.example.com
Password:
$
```

Use the `config` command to change settings (example options):

```
$ config

usage: config [ -ahv ] [ -d id ] [ -g id ] [ -p path ] [ -r configurator ] [ -s id=value ] [ -P id ]
 -a --run-all		Run all registered configurators.
 -h --help		Display this message.
 -v --verbose		Log extra debug info.
 -T --trace		Log extra trace info.
 -d --del=id		Remove the given config element.
 -g --get=id		Display the value of a config element.
 -p --path		Use an alternate config file.
 -r --run=configurator	Run the specified registered configurator.
 -s --set=id=value	Change the value of config element.
 -e --export=file	Save active configuration to file.
 -i --import=file	Load configuration from file.
 -t --test-import=file	Pretend to load configuration from file.
 -I --strict-import=file	Load configuration from file only if vendor and product are correct.
 -S --separator=char	The pattern to separate fields with, default is '.'.
 -P --password=id	Prompt for a user password, encrypt it, then save it in 'id'.
```

Display settings on `port 33`:

```
$ config -g config.ports.port33
config.ports.port33.charsize 8
config.ports.port33.dtrmode alwayson
config.ports.port33.flowcontrol None
config.ports.port33.label Port 33
config.ports.port33.loglevel 1
config.ports.port33.mode portmanager
config.ports.port33.parity None
config.ports.port33.pinout X1
config.ports.port33.protocol RS232
config.ports.port33.speed 9600
config.ports.port33.ssh on
config.ports.port33.stop 1
config.ports.port33.syslog.facility Default
config.ports.port33.syslog.priority Default
config.ports.port33.terminal vt220
config.ports.port33.webshell on
```

On port 33, set the name and speed:

```text
config -s config.ports.port33.label="Infoblox 1405 RMA Portal"
config -s config.ports.port33.speed=115200
```

Validate the settings changed:

```
$ config -g config.ports.port33
config.ports.port33.charsize 8
config.ports.port33.dtrmode alwayson
config.ports.port33.flowcontrol None
config.ports.port33.label Infoblox 1405 RMA Portal
config.ports.port33.loglevel 1
config.ports.port33.mode portmanager
config.ports.port33.parity None
config.ports.port33.pinout X1
config.ports.port33.protocol RS232
config.ports.port33.speed 115200
config.ports.port33.ssh on
config.ports.port33.stop 1
config.ports.port33.syslog.facility Default
config.ports.port33.syslog.priority Default
config.ports.port33.terminal vt220
config.ports.port33.webshell on
```

Apply the configuration with `config -r serialconfig`:

```
$ config -r serialconfig
- Configurator 'ups' ran in 0 seconds.
- Configurator 'enviro' ran in 0 seconds.
- Configurator 'time' ran in 0 seconds.
- Configurator 'services' ran in 0 seconds.
- Configurator 'serialconfig' ran in 0 seconds.
- Configurator 'console' ran in 0 seconds.
- Configurator 'smtp' ran in 0 seconds.
No resolv.conf for interface eth1.dhcp
No resolv.conf for interface eth0.dhcp
- Configurator 'ipconfig' ran in 1 seconds.
- Configurator 'dialin' ran in 0 seconds.
- Configurator 'modemwatchdog' ran in 0 seconds.
- Configurator 'users' ran in 0 seconds.
- Configurator 'secrets' ran in 0 seconds.
- Configurator 'conman' ran in 0 seconds.
- Configurator 'hosts' ran in 1 seconds.
- Configurator 'nagios' ran in 0 seconds.
- Configurator 'power' ran in 0 seconds.
- Configurator 'cascade' ran in 0 seconds.
- Configurator 'sshconfig' ran in 0 seconds.
- Configurator 'powersupplies' ran in 0 seconds.
- Configurator 'intsensors' ran in 0 seconds.
- Configurator 'autoresponse' ran in 1 seconds.
- Configurator 'snmp' ran in 0 seconds.
- Configurator 'sshforwards' ran in 0 seconds.
- Configurator 'ssl' ran in 0 seconds.
- Configurator 'forwards' ran in 0 seconds.
- Configurator 'masq' ran in 0 seconds.
- Configurator 'portrules' ran in 0 seconds.
- Configurator 'dhcp' ran in 0 seconds.
- Configurator 'systemlog' ran in 0 seconds.
```