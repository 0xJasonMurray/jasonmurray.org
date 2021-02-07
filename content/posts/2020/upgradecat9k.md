---
title: "Upgrading a Cisco 9300 to 17.3.1"
date: 2020-10-22T09:52:21-05:00
toc: false
images:
categories:
  - tech
tags: 
  - cisco
  - 9k
---

# Overview

Steps necessary to upgrade a Cisco 9300 (C9300L-48UXG-4X) from 16.12.x to 17.3.1.

# Details

Remove old files:

```
9300-1#install remove inactive
install_remove: START Thu Oct 22 14:50:36 UTC 2020
Cleaning up unnecessary package files
No path specified, will use booted path flash:packages.conf
Cleaning flash:
  Scanning boot directory for packages ... done.
  Preparing packages list to delete ...
    cat9k-cc_srdriver.16.12.02.SPA.pkg
      File is in use, will not delete.
    cat9k-espbase.16.12.02.SPA.pkg
      File is in use, will not delete.
    cat9k-guestshell.16.12.02.SPA.pkg
      File is in use, will not delete.
    cat9k-rpbase.16.12.02.SPA.pkg
      File is in use, will not delete.
    cat9k-rpboot.16.12.02.SPA.pkg
      File is in use, will not delete.
    cat9k-sipbase.16.12.02.SPA.pkg
      File is in use, will not delete.
    cat9k-sipspa.16.12.02.SPA.pkg
      File is in use, will not delete.
    cat9k-srdriver.16.12.02.SPA.pkg
      File is in use, will not delete.
    cat9k-webui.16.12.02.SPA.pkg
      File is in use, will not delete.
    cat9k-wlc.16.12.02.SPA.pkg
      File is in use, will not delete.
    packages.conf
      File is in use, will not delete.
  done.

The following files will be deleted:
[switch 1]:
/flash/cat9k_iosxe.17.03.01.SPA.bin

Do you want to remove the above files? [y/n]y

[switch 1]:
Deleting file flash:cat9k_iosxe.17.03.01.SPA.bin ... done.
SUCCESS: Files deleted.
--- Starting Post_Remove_Cleanup ---
Performing Post_Remove_Cleanup on all members
  [1] Post_Remove_Cleanup package(s) on switch 1
  [1] Finished Post_Remove_Cleanup on switch 1
Checking status of Post_Remove_Cleanup on [1]
Post_Remove_Cleanup: Passed on [1]
Finished Post_Remove_Cleanup

SUCCESS: install_remove  Thu Oct 22 14:51:41 UTC 2020
```

Copy new image to flash (265Kb/s is S L O W):

```
jemurray@mbp-2019:~/Downloads $ scp cat9k_iosxe.17.03.01.SPA.bin 192.168.86.44:cat9k_iosxe.17.03.01.SPA.bin
Password:
cat9k_iosxe.17.03.01.SPA.bin    100%  851MB 265.2KB/s   54:45
Connection to 192.168.86.44 closed by remote host.
```

Validate the transfer was successful:

```
9300-1#dir flash:cat9k_iosxe.17.03.01.SPA.bin
Directory of flash:/cat9k_iosxe.17.03.01.SPA.bin

335896  -rw-        892195673  Oct 22 2020 15:47:18 +00:00  cat9k_iosxe.17.03.01.SPA.bin

11353194496 bytes total (8987889664 bytes free)
```

Validate boot options:

- BOOT variable: flash:packages.conf
- No manual boot

```
9300-1#show boot
---------------------------
Switch 1
---------------------------
Current Boot Variables:
BOOT variable does not exist

Boot Variables on next reload:
BOOT variable = flash:packages.conf
Manual Boot = no
Enable Break = no
Boot Mode = DEVICE
iPXE Timeout = 0
```

Start the upgrade:

```
9300-1#install add file flash:cat9k_iosxe.17.03.01.SPA.bin activate commit
install_add_activate_commit: START Thu Oct 22 15:53:26 UTC 2020
install_add_activate_commit: Adding PACKAGE
install_add_activate_commit: Checking whether new add is allowed ....

--- Starting initial file syncing ---
Info: Finished copying flash:cat9k_iosxe.17.03.01.SPA.bin to the selected switch(es)
Finished initial file syncing

--- Starting Add ---
Performing Add on all members
  [1] Add package(s) on switch 1
  [1] Finished Add on switch 1
Checking status of Add on [1]
Add: Passed on [1]
Finished Add

Image added. Version: 17.03.01.0.351
install_add_activate_commit: Activating PACKAGE
Following packages shall be activated:
/flash/cat9k-wlc.17.03.01.SPA.pkg
/flash/cat9k-webui.17.03.01.SPA.pkg
/flash/cat9k-srdriver.17.03.01.SPA.pkg
/flash/cat9k-sipspa.17.03.01.SPA.pkg
/flash/cat9k-sipbase.17.03.01.SPA.pkg
/flash/cat9k-rpboot.17.03.01.SPA.pkg
/flash/cat9k-rpbase.17.03.01.SPA.pkg
/flash/cat9k-lni.17.03.01.SPA.pkg
/flash/cat9k-guestshell.17.03.01.SPA.pkg
/flash/cat9k-espbase.17.03.01.SPA.pkg
/flash/cat9k-cc_srdriver.17.03.01.SPA.pkg

This operation may require a reload of the system. Do you want to proceed? [y/n]y
--- Starting Activate ---
Performing Activate on all members
  [1] Activate package(s) on switch 1
    --- Starting list of software package changes ---
    Old files list:
      Removed cat9k-cc_srdriver.16.12.02.SPA.pkg
      Removed cat9k-espbase.16.12.02.SPA.pkg
      Removed cat9k-guestshell.16.12.02.SPA.pkg
      Removed cat9k-rpbase.16.12.02.SPA.pkg
      Removed cat9k-rpboot.16.12.02.SPA.pkg
      Removed cat9k-sipbase.16.12.02.SPA.pkg
      Removed cat9k-sipspa.16.12.02.SPA.pkg
      Removed cat9k-srdriver.16.12.02.SPA.pkg
      Removed cat9k-webui.16.12.02.SPA.pkg
      Removed cat9k-wlc.16.12.02.SPA.pkg
    New files list:
      Added cat9k-cc_srdriver.17.03.01.SPA.pkg
      Added cat9k-espbase.17.03.01.SPA.pkg
      Added cat9k-guestshell.17.03.01.SPA.pkg
      Added cat9k-lni.17.03.01.SPA.pkg
      Added cat9k-rpbase.17.03.01.SPA.pkg
      Added cat9k-rpboot.17.03.01.SPA.pkg
      Added cat9k-sipbase.17.03.01.SPA.pkg
      Added cat9k-sipspa.17.03.01.SPA.pkg
      Added cat9k-srdriver.17.03.01.SPA.pkg
      Added cat9k-webui.17.03.01.SPA.pkg
      Added cat9k-wlc.17.03.01.SPA.pkg
    Finished list of software package changes
  [1] Finished Activate on switch 1
Checking status of Activate on [1]
Activate: Passed on [1]
Finished Activate

--- Starting Commit ---
Performing Commit on all members
  [1] Commit package(s) on switch 1
  [1] Finished Commit on switch 1
Checking status of Commit on [1]
Commit: Passed on [1]
Finished Commit

[1]: Performing Upgrade_Service
300+0 records in
300+0 records out
307200 bytes (307 kB, 300 KiB) copied, 0.373338 s, 823 kB/s
16128+0 records in
16128+0 records out
8257536 bytes (8.3 MB, 7.9 MiB) copied, 10.0267 s, 824 kB/s
  SUCCESS: Upgrade_Service finished
Install will reload the system now!
SUCCESS: install_add_activate_commit  Thu Oct 22 16:03:09 UTC 2020
```

Console output from upgrade:

```
*Oct 22 15:53:27.891: %INSTALL-5-INSTALL_START_INFO: Switch 1 R0/0: install_engine: Started install one-shot flash:cat9k_iosxe.17.03.01.SPA.bin
*Oct 22 16:00:36.796: %INSTALL-5-INSTALL_AUTO_ABORT_TIMER_PROGRESS: Switch 1 R0/0: rollback_timer: Install auto abort timer will expire in 7200 seconds%IOSXEBOOT-4-BOOTLOADER_UPGRADE: (local/local): Starting boot preupgrade
                                        %IOSXEBOOT-4-BOOTLOADER_UPGRADE: (local/local): ### Thu Oct 22 16:01:12 UTC 2020 PLEASE DO NOT POWER CYCLE ### BOOT LOADER UPGRADING
                                                                                                                                                                            %IOSXEBOOT-4-flashcp: (local/local): polaris_franklin_rommon.bin
                                                     %IOSXEBOOT-4-BOOTLOADER_UPGRADE: (local/local): boot loader upgrade successful
                                                                                                                                   MM [0] MCU version 41 sw ver 34


                                                                                                                                                                  MCU UPGRADE IN PROGRESS... PLEASE DO NOT POWER CYCLE!!

                                 Front-end Microcode IMG MGR: found 2 microcode images for 1 device.
                                                                                                    Image for front-end 0: /tmp/microcode_update/front_end/fe_type_10_1 update needed: yes
   Image for front-end 0: /tmp/microcode_update/front_end/fe_type_10_3 update needed: no

                                                                                        Front-end Microcode IMG MGR: Preparing to program device microcode...
                                                                                                                                                             Front-end Microcode IMG MGR: Preparing to program device[0], index=0 ...85684 bytes.
                                                          Front-end Microcode IMG MGR: Programming app image for device 0...
                                                                                                                            MCU is in bootlader mode:Current App version 34

                                                                                                                                                                           External MCU Reset
      Front-end Microcode IMG MGR: MCU already in bootloader mode
                                                                 0%!!!!!!!!!!!!!!!!
                                                                                   10%!!!!!!!!!!!!!!!
                                                                                                     20%!!!!!!!!!!!!!!!
                                                                                                                       30%!!!!!!!!!!!!!!!!
                                                                                                                                          40%!!!!!!!!!!!!!!!
                                                                                                                                                            50%!!!!!!!!!!!!!!!
                                                                                                                                                                              60%!!!!!!!!!!!!!!!!
          70%!!!!!!!!!!!!!!!
                            80%!!!!!!!!!!!!!!!
                                              90%!!!!!!!!!!!!!!!!
                                                                  resetting MCU after download:Current bl version 107
                                                                                                                     Soft reset MCU via bl#
                                                                                                                                           Front-end Microcode IMG MGR: Microcode programming complete for device 0.
                             Front-end Microcode IMG MGR: Preparing to program device[0], index=1 ...11044 bytes.... Skipped[1].
                                                                                                                                Front-end Microcode IMG MGR: Microcode programming complete in 75 seconds


                  MCU UPGRADE COMPLETED!!...
*Oct 22 16:03:09.770: %INSTALL-5-INSTALL_COMPLETED_INFO: Switch 1 R0/0: install_engine: Completed install one-shot PACKAGE flash:cat9k_iosxe.17.03.01.SPA.bin
Chassis 1 reloading, reason - Reload command
reload fp action requested
                          rp processes exit with reload switch code

```

Console during reboot:

```
Initializing Hardware...

System Bootstrap, Version 17.1.1r [FC1], RELEASE SOFTWARE (P)
Compiled Tue 08/20/2019 16:14:45.96 by rel

Current ROMMON image : Primary
Last reset cause     : LocalSoft
C9300L-48UXG-4X platform with 8388608 Kbytes of main memory

boot: attempting to boot from [flash:packages.conf]
boot: reading file packages.conf
#
###############################################################################################################################################################################################################################################################################################################################################################################################################################################################################



Both links down, not waiting for other switches
Switch number is 1

              Restricted Rights Legend

Use, duplication, or disclosure by the Government is
subject to restrictions as set forth in subparagraph
(c) of the Commercial Computer Software - Restricted
Rights clause at FAR sec. 52.227-19 and subparagraph
(c) (1) (ii) of the Rights in Technical Data and Computer
Software clause at DFARS sec. 252.227-7013.

           Cisco Systems, Inc.
           170 West Tasman Drive
           San Jose, California 95134-1706



Cisco IOS Software [Amsterdam], Catalyst L3 Switch Software (CAT9K_IOSXE), Version 17.3.1, RELEASE SOFTWARE (fc5)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2020 by Cisco Systems, Inc.
Compiled Fri 07-Aug-20 21:32 by mcpre


This software version supports only Smart Licensing as the software licensing mechanism.


PLEASE READ THE FOLLOWING TERMS CAREFULLY. INSTALLING THE LICENSE OR
LICENSE KEY PROVIDED FOR ANY CISCO SOFTWARE PRODUCT, PRODUCT FEATURE,
AND/OR SUBSEQUENTLY PROVIDED SOFTWARE FEATURES (COLLECTIVELY, THE
"SOFTWARE"), AND/OR USING SUCH SOFTWARE CONSTITUTES YOUR FULL
ACCEPTANCE OF THE FOLLOWING TERMS. YOU MUST NOT PROCEED FURTHER IF YOU
ARE NOT WILLING TO BE BOUND BY ALL THE TERMS SET FORTH HEREIN.

Your use of the Software is subject to the Cisco End User License Agreement
(EULA) and any relevant supplemental terms (SEULA) found at
http://www.cisco.com/c/en/us/about/legal/cloud-and-software/software-terms.html.

You hereby acknowledge and agree that certain Software and/or features are
licensed for a particular term, that the license to such Software and/or
features is valid only for the applicable term and that such Software and/or
features may be shut down or otherwise terminated by Cisco after expiration
of the applicable license term (e.g., 90-day trial period). Cisco reserves
the right to terminate any such Software feature electronically or by any
other means available. While Cisco may provide alerts, it is your sole
responsibility to monitor your usage of any such term Software feature to
ensure that your systems and networks are prepared for a shutdown of the
Software feature.



FIPS: Flash Key Check : Key Not Found, FIPS Mode Not Enabled

All TCP AO KDF Tests Pass
cisco C9300L-48UXG-4X (X86) processor with 1332155K/6147K bytes of memory.
Processor board ID FOC2402L9M4
2048K bytes of non-volatile configuration memory.
8388608K bytes of physical memory.
1638400K bytes of Crash Files at crashinfo:.
11264000K bytes of Flash at flash:.
8388608K bytes of USB Flash at usbflash0:.

Base Ethernet MAC Address          : c4:b2:39:d9:d3:80
Motherboard Assembly Number        : 73-19176-07
Motherboard Serial Number          : FOC23501HHP
Model Revision Number              : A0
Motherboard Revision Number        : A0
Model Number                       : C9300L-48UXG-4X
System Serial Number               : FOC2402L9M4
CLEI Code Number                   :




Press RETURN to get started!


*Oct 22 16:06:06.643: %SMART_LIC-6-EXPORT_CONTROLLED: Usage of export controlled features is not allowed
*Oct 22 16:06:07.064: %IOSD_INFRA-6-IFS_DEVICE_OIR: Device usbflash0 added
*Oct 22 16:06:08.211: %SMART_LIC-6-AGENT_READY: Smart Agent for Licensing is initialized
*Oct 22 16:06:08.212: %SMART_LIC-6-AGENT_ENABLED: Smart Agent for Licensing is enabled
*Oct 22 16:06:08.213: %SMART_LIC-6-EXPORT_CONTROLLED: Usage of export controlled features is not allowed
*Oct 22 16:06:08.270: %SMART_LIC-5-EVAL_START: Entering evaluation period
*Oct 22 16:06:08.349: %SMART_LIC-5-EVAL_START: Entering evaluation period
*Oct 22 16:06:11.378: pagp init: platform supports EC/LACP xFSURA Tracing tool registry return: 0
*Oct 22 16:06:11.419: LACP-GR: infra cb, GR_NONE

*Oct 22 16:06:11.420: BFD: brace register success
*Oct 22 16:06:13.934: %CRYPTO-4-AUDITWARN: Encryption audit check could not be performed
*Oct 22 16:06:13.934: %CRYPTO_ENGINE-4-CSDL_COMPLIANCE_DISABLED: Cisco PSB security compliance has been disabled
*Oct 22 16:06:13.954: %SPANTREE-5-EXTENDED_SYSID: Extended SysId enabled for type vlan
*Oct 22 16:06:14.357: %LINK-3-UPDOWN: Interface Lsmpi18/3, changed state to up
*Oct 22 16:06:14.369: %LINK-3-UPDOWN: Interface EOBC18/1, changed state to up
*Oct 22 16:06:14.369: %LINEPROTO-5-UPDOWN: Line protocol on Interface LI-Null0, changed state to up
*Oct 22 16:06:14.370: %LINK-3-UPDOWN: Interface GigabitEthernet0/0, changed state to down
*Oct 22 16:06:14.370: %LINK-3-UPDOWN: Interface LIIN18/2, changed state to up
*Oct 22 16:06:14.370: %LINK-5-CHANGED: Interface Bluetooth0/4, changed state to administratively down
*Oct 22 16:06:14.477: %PNP-6-PNP_DISCOVERY_STARTED: PnP Discovery started
*Oct 22 16:06:14.481: %HMANRP-6-HMAN_IOS_CHANNEL_INFO: HMAN-IOS channel event for switch 1: EMP_RELAY: Channel UP!
*Oct 22 16:05:57.705: %STACKMGR-6-STACK_LINK_CHANGE: Switch 1 R0/0: stack_mgr: Stack port 1 on Switch 1 is cable-not-connected
*Oct 22 16:05:57.705: %STACKMGR-6-STACK_LINK_CHANGE: Switch 1 R0/0: stack_mgr: Stack port 2 on Switch 1 is down
*Oct 22 16:05:57.705: %STACKMGR-6-STACK_LINK_CHANGE: Switch 1 R0/0: stack_mgr: Stack port 2 on Switch 1 is cable-not-connected
*Oct 22 16:05:57.705: %STACKMGR-4-SWITCH_ADDED: Switch 1 R0/0: stack_mgr: Switch 1 has been added to the stack.
*Oct 22 16:05:59.798: %STACKMGR-4-SWITCH_ADDED: Switch 1 R0/0: stack_mgr: Switch 1 has been added to the stack.
*Oct 22 16:06:01.799: %STACKMGR-4-SWITCH_ADDED: Switch 1 R0/0: stack_mgr: Switch 1 has been added to the stack.
*Oct 22 16:06:01.799: %STACKMGR-6-ACTIVE_ELECTED: Switch 1 R0/0: stack_mgr: Switch 1 has been elected ACTIVE.
*Oct 22 16:06:14.493: %SMART_LIC-6-HA_ROLE_CHANGED: Smart Agent HA role changed to Active.
*Oct 22 16:06:15.146: %SYS-5-CONFIG_P: Configured programmatically by process MGMT VRF Process from console as vty0
*Oct 22 16:06:15.432: %IOSXE_MGMTVRF-6-CREATE_SUCCESS_INFO: Management vrf Mgmt-vrf created with ID 1, ipv4 table-id 0x1, ipv6 table-id 0x1E000001
*Oct 22 16:06:15.433: %SYS-5-CONFIG_P: Configured programmatically by process MGMT VRF Process from console as vty0
*Oct 22 16:06:15.477: %IOSXE_MGMTVRF-6-CREATE_SUCCESS_INFO: Management vrf Mgmt-vrf created with ID 1, ipv4 table-id 0x1, ipv6 table-id 0x1E000001
*Oct 22 16:06:15.477: %SYS-5-CONFIG_P: Configured programmatically by process MGMT VRF Process from console as vty0
*Oct 22 16:06:15.486: %LINEPROTO-5-UPDOWN: Line protocol on Interface Lsmpi18/3, changed state to up
*Oct 22 16:06:15.487: %LINEPROTO-5-UPDOWN: Line protocol on Interface EOBC18/1, changed state to up
*Oct 22 16:06:15.487: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to down
*Oct 22 16:06:15.487: %LINEPROTO-5-UPDOWN: Line protocol on Interface LIIN18/2, changed state to up
*Oct 22 16:06:15.487: %LINEPROTO-5-UPDOWN: Line protocol on Interface Bluetooth0/4, changed state to down
*Oct 22 16:06:16.070: %SYS-7-NVRAM_INIT_WAIT_TIME: Waited 0 seconds for NVRAM to be available
*Oct 22 16:06:16.120: %CRYPTO_ENGINE-5-KEY_ADDITION: A key named TP-self-signed-2554541882 has been generated or imported by crypto config
*Oct 22 16:06:16.128: %CRYPTO_ENGINE-5-KEY_ADDITION: A key named 9300-1.example.com has been generated or imported by crypto config
*Oct 22 16:06:16.180: %ONEP_BASE-6-SS_ENABLED: ONEP: Service set Base was enabled by Default
*Oct 22 16:06:16.207: %PKI-6-TRUSTPOINT_CREATE: Trustpoint: SLA-TrustPoint created succesfully
*Oct 22 16:06:16.236: %PKI-6-TRUSTPOINT_CREATE: Trustpoint: TP-self-signed-2554541882 created succesfully
*Oct 22 16:06:16.455: %SYS-5-CONFIG_I: Configured from memory by console
*Oct 22 16:06:16.549: %SYS-5-CONFIG_P: Configured programmatically by process CPP MQC QoS Policy manager from console as vty1
*Oct 22 16:06:16.567: %PLATFORM_FEP-6-FRU_PS_OIR: Switch 1: FRU power supply A inserted
*Oct 22 16:06:16.568: %PLATFORM_FEP-6-FRU_PS_OIR: Switch 1: FRU power supply B inserted
*Oct 22 16:06:16.619: %LINEPROTO-5-UPDOWN: Line protocol on Interface Vlan1, changed state to down
*Oct 22 16:06:17.072: %SSH-5-ENABLED: SSH 2.0 has been enabled
*Oct 22 16:06:16.957: %UICFGEXP-6-SERVER_NOTIFIED_START: Switch 1 R0/0: psd: Server iox has been notified to start
*Oct 22 16:06:17.251: %SYS-5-RESTART: System restarted --
Cisco IOS Software [Amsterdam], Catalyst L3 Switch Software (CAT9K_IOSXE), Version 17.3.1, RELEASE SOFTWARE (fc5)
Technical Support: http://www.cisco.com/techsupport
Copyright (c) 1986-2020 by Cisco Systems, Inc.
Compiled Fri 07-Aug-20 21:32 by mcpre
*Oct 22 16:06:17.660: ILP:: POE POST for switch 1: PASS
*Oct 22 16:06:18.928: %CRYPTO_ENGINE-5-KEY_ADDITION: A key named TP-self-signed-2554541882.server has been generated or imported by crypto-engine
*Oct 22 16:06:19.015: %LINK-3-UPDOWN: Interface Vlan1, changed state to up
*Oct 22 16:06:22.603: %PNP-6-PNP_BEST_UDI_UPDATE: Best UDI [PID:C9300L-48UXG-4X,VID:A0,SN:FOC2402L9M4] identified via (master-registry)
*Oct 22 16:06:22.603: %PNP-6-PNP_CDP_UPDATE: Device UDI [PID:C9300L-48UXG-4X,VID:A0,SN:FOC2402L9M4] identified for CDP
*Oct 22 16:06:22.604: %PNP-6-PNP_DISCOVERY_STOPPED: PnP Discovery stopped (Startup Config Present)
*Oct 22 16:06:23.116: %LINK-3-UPDOWN: Interface GigabitEthernet0/0, changed state to up
*Oct 22 16:06:24.116: %LINEPROTO-5-UPDOWN: Line protocol on Interface GigabitEthernet0/0, changed state to up
*Oct 22 16:06:30.546: %DHCP-6-ADDRESS_ASSIGN: Interface GigabitEthernet0/0 assigned DHCP address 192.168.86.44, mask 255.255.255.0, hostname 9300-1

*Oct 22 16:06:34.108: %PKI-2-NON_AUTHORITATIVE_CLOCK: PKI functions can not be initialized until an authoritative time source, like NTP, can be obtained.
*Oct 22 16:06:34.120: %PKI-6-TRUSTPOINT_CREATE: Trustpoint: CISCO_IDEVID_SUDI_LEGACY created succesfully
*Oct 22 16:06:34.130: %CRYPTO_ENGINE-5-KEY_ADDITION: A key named CISCO_IDEVID_SUDI_LEGACY has been generated or imported by pki-sudi
*Oct 22 16:06:34.157: %PKI-6-TRUSTPOINT_CREATE: Trustpoint: CISCO_IDEVID_SUDI_LEGACY0 created succesfully
*Oct 22 16:06:34.160: %PKI-6-TRUSTPOINT_CREATE: Trustpoint: CISCO_IDEVID_SUDI created succesfully
*Oct 22 16:06:34.174: %CRYPTO_ENGINE-5-KEY_ADDITION: A key named CISCO_IDEVID_SUDI has been generated or imported by pki-sudi
*Oct 22 16:06:34.222: %PKI-6-TRUSTPOINT_CREATE: Trustpoint: CISCO_IDEVID_SUDI0 created succesfully
*Oct 22 16:06:33.977: %IOX-3-IOX_RESTARTABITLITY: Switch 1 R0/0: run_ioxn_caf: Stack is in N+1 mode, disabling sync for IOx restartability
*Oct 22 16:06:41.323: %EVENT_TRACE-6-ENABLE: Trace already enabled.
*Oct 22 16:06:41.323: %EVENT_TRACE-6-ENABLE: Trace already enabled.
*Oct 22 16:06:41.323: %EVENT_TRACE-6-ENABLE: Trace already enabled.
*Oct 22 16:06:41.323: %EVENT_TRACE-6-ENABLE: Trace already enabled.
*Oct 22 16:06:44.622: %IM-6-IOX_ENABLEMENT: Switch 1 R0/0: ioxman: IOX is ready.
*Oct 22 16:06:48.263: %SMART_LIC-6-EXPORT_CONTROLLED: Usage of export controlled features is not allowed
*Oct 22 16:06:48.263: %CALL_HOME-6-CALL_HOME_ENABLED: Call-home is enabled by Smart Agent for Licensing.
*Oct 22 16:06:48.903: %LINK-3-UPDOWN: Interface AppGigabitEthernet1/0/1, changed state to up
*Oct 22 16:06:49.903: %LINEPROTO-5-UPDOWN: Line protocol on Interface AppGigabitEthernet1/0/1, changed state to up

User Access Verification

Username:
```

