---
title: "Testing Radius EAP/PEAP from the command line"
date: 2020-08-29T18:36:18-05:00
toc: false
images:
categories:
  - tech
tags: 
  - radius
  - eapol_test
  - wpa_supplicant
---

The `eapol_test` command is used to perform a variety of EAP authentication requests within Radius from the command line.  This software is found in the [wpa_supplicant](https://w1.fi/wpa_supplicant/) project.

Tools like this are helpful for production monitoring or remote troubleshooting.  


First, create a EAP/PEAP generic configuration file named eapol_test.cfg:

```
network={
  ssid="eduroam"
  key_mgmt=IEEE8021X
  eap=PEAP
  pairwise=CCMP TKIP
  group=CCMP TKIP WEP104 WEP40
  phase2="auth=MSCHAPV2"
  identity="jemurray"
  anonymous_identity="jemurray"
  password="MyPa$$w0rd"
}
```

Run the `eapol_test` command using the configuration file, remote host IP address, radius port, and shared secret:

```
sudo eapol_test -c ./eapol_test.cfg -a 192.168.0.1 -p 1812 -s r4d1u$sh4r3dSecr3t
```

The results of the Radius request are printed out in verbose detail.  In this example, the Radius request was successful:

```
....lots of output....

RADIUS packet matching with station
MS-MPPE-Send-Key (sign) - hexdump(len=32): eb 87 75 0e 2b ba 87 b5 ef ff ff ff ff ff ff ff ff ff ff dc af 3a ea 78 f5 c9 ee c0 17 84 9f 09
MS-MPPE-Recv-Key (crypt) - hexdump(len=32): cd c4 dd 6a 7d ff ff ff ff ff ff ff ff ff ff ff 3a cb 6c eb 47 fa c0 07 ec ef 72 9c 80 51 ef 92
decapsulated EAP packet (code=3 id=242 len=4) from RADIUS server: EAP Success
EAPOL: Received EAP-Packet frame
EAPOL: SUPP_BE entering state REQUEST
EAPOL: getSuppRsp
EAP: EAP entering state RECEIVED
EAP: Received EAP-Success
EAP: Status notification: completion (param=success)
EAP: EAP entering state SUCCESS
CTRL-EVENT-EAP-SUCCESS EAP authentication completed successfully
EAPOL: IEEE 802.1X for plaintext connection; no EAPOL-Key frames required
WPA: EAPOL processing complete
EAPOL: SUPP_PAE entering state AUTHENTICATED
EAPOL: SUPP_BE entering state RECEIVE
EAPOL: SUPP_BE entering state SUCCESS
EAPOL: SUPP_BE entering state IDLE
eapol_sm_cb: result=1
EAPOL: Successfully fetched key (len=32)
PMK from EAPOL - hexdump(len=32): cd c4 dd 6a 7d fd ff ff ff ff ff ff ff ff ff ff ff cb 6c eb 47 fa c0 07 ec ff 72 ff ff ff ff ff
No EAP-Key-Name received from server
EAP: deinitialize previously used EAP method (25, PEAP) at EAP deinit
ENGINE: engine deinit
MPPE keys OK: 1  mismatch: 0
SUCCESS
```