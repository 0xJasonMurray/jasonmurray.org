---
title: "Netmiko, Napalm, Nornir supported devices"
date: 2020-07-24T12:30:49-05:00
toc: false
images:
categories:
  - tech
tags: 
  - napalm
  - netmiko
  - nornir
---

Netmiko, Napalm, and Nornir all use drivers or connection handlers for establishing remote connectivity to networking devices.  The keywords needed to enable remote sessions was not obvious to find.  Here is a consolidated list of connection keyword types necessary to load the proper modules.  

Each framework is a little different.  Netmiko for example uses connection handlers, Napalm uses drivers, and Nornir uses a combination of Netmiko or Napalm depending on which function is selected.


Netmiko connection handler types for the variable: `device_type="cisco_ios"`:

```
a10
accedian
alcatel_aos
alcatel_sros
apresia_aeos
arista_eos
aruba_os
avaya_ers
avaya_vsp
brocade_fastiron
brocade_netiron
brocade_nos
brocade_vdx
brocade_vyos
calix_b6
checkpoint_gaia
ciena_saos
cisco_asa
cisco_ios
cisco_nxos
cisco_s300
cisco_tp
cisco_wlc
cisco_xe
cisco_xr
cloudgenix_ion
coriant
dell_dnos9
dell_force10
dell_isilon
dell_os10
dell_os6
dell_os9
dell_powerconnect
dlink_ds
eltex
eltex_esr
endace
enterasys
extreme
extreme_ers
extreme_exos
extreme_netiron
extreme_nos
extreme_slx
extreme_vdx
extreme_vsp
extreme_wing
f5_linux
f5_ltm
f5_tmsh
flexvnf
fortinet
generic_termserver
hp_comware
hp_procurve
huawei
huawei_olt
huawei_smartax
huawei_vrpv8
ipinfusion_ocnos
juniper
juniper_junos
juniper_screenos
keymile
keymile_nos
linux
mellanox
mellanox_mlnxos
mikrotik_routeros
mikrotik_switchos
mrv_lx
mrv_optiswitch
netapp_cdot
netscaler
nokia_sros
oneaccess_oneos
ovs_linux
paloalto_panos
pluribus
quanta_mesh
rad_etx
ruckus_fastiron
ruijie_os
sophos_sfos
ubiquiti_edge
ubiquiti_edgeswitch
ubiquiti_unifiswitch
vyatta_vyos
vyos
watchguard_fireware
```

The list of netmiko supported devices is generated with this script:

```
from netmiko import ConnectHandler

# Just pick an 'invalid' device_type
cisco1 = {
    "device_type": "invalid",
    "host": "cisco1.lasthop.io",
    "username": "pyclass",
    "password": "invalid"
}

net_connect = ConnectHandler(**cisco1)
net_connect.disconnect()
```

Napalm driver types for the variable: `driver = get_network_driver("ios")`:

```
eos
junos
iosxr
nxos
nxos_ssh
ios
```

Napalm supported devices pulled from: https://napalm.readthedocs.io/en/latest/support/