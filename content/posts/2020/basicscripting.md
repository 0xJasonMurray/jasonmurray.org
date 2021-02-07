---
title: "Basic script writing"
date: 2020-06-21T07:52:54-05:00
toc: false
images:
categories:
  - tech
tags: 
  - scripting
  - programming
  - automation
---

Basic script writing is a tool any person who performs repetitive tasks on a computer should posses.   Scripting comes in many forms from simple `bash` scripts to more complex `python` scripts.  These small snippets of code help reduce the monotony of manually running the same command over and over again.

Let's use the following example. At WashU there are 5 DNS servers.   Validating DNS is operating as expected during maintenance events is a common task.

Manually validating DNS is returning a proper answer on all servers requires running a DNS lookup tool such as `host` for each system over and over.  This is tedious to run in rapid succession during the maintenance event:

```
jemurray@linux-host:~$ host wustl.edu 128.252.0.1
Using domain server:
Name: 128.252.0.1
Address: 128.252.0.1#53
Aliases:

wustl.edu has address 128.252.160.5
wustl.edu mail is handled by 10 smtp3.mx.wustl.edu.
wustl.edu mail is handled by 10 smtp2.mx.wustl.edu.
wustl.edu mail is handled by 10 smtp1.mx.wustl.edu.
wustl.edu mail is handled by 10 smtp4.mx.wustl.edu.
jemurray@linux-host:~$ host wustl.edu 128.252.0.9
Using domain server:
Name: 128.252.0.9
Address: 128.252.0.9#53
Aliases:

wustl.edu has address 128.252.160.5
wustl.edu mail is handled by 10 smtp1.mx.wustl.edu.
wustl.edu mail is handled by 10 smtp4.mx.wustl.edu.
wustl.edu mail is handled by 10 smtp3.mx.wustl.edu.
wustl.edu mail is handled by 10 smtp2.mx.wustl.edu.

...
```

Fourteen lines of code (minus comments), reduces this effort into a single command:

```
#!/bin/bash

# All DNS servers + a fake one to simulate failure
dns="128.252.0.1 128.252.0.9 128.252.0.17 128.252.0.25 128.252.0.100 128.252.0.123"

# Run the script until ctrl+c breaks out of the while look
echo "running forever, CTRL+C to quit"
while [ 1 ]; do

  # Iterate over all DNS servers
	for i in $dns; do
    # Run the host command, place the output in a variable
		output=`host -W 1 wustl.edu $i`

    # check to see if the command succeeded ($? == 0) or failed ($? != 0)
    # print the status
		if [ $? -ne 0 ]; then
			echo "Failed server `date`: $i: $output"
		else
			echo "Server $i good"
		fi
	done
  # Run the command every second.  Try doing that by hand over all servers.
	sleep 1
done
```

Executing the command outputs both success and failures:

```
jemurray@linux-host:~$ ./simplednscheck.sh
running forever, CTRL+C to quit
Server 128.252.0.1 good
Server 128.252.0.9 good
Server 128.252.0.17 good
Server 128.252.0.25 good
Server 128.252.0.100 good
Failed server Sun Jun 21 09:07:12 CDT 2020: 128.252.0.123: ;; connection timed out; no servers could be reached
Server 128.252.0.1 good
Server 128.252.0.9 good
Server 128.252.0.17 good
Server 128.252.0.25 good
Server 128.252.0.100 good
```

Piping to `grep` reduces human intervention yet again, by only displaying the failure events:

```
jemurray@linux-host:~$ ./simplednscheck.sh | grep Failed
Failed server Sun Jun 21 09:08:26 CDT 2020: 128.252.0.123: ;; connection timed out; no servers could be reached
Failed server Sun Jun 21 09:08:29 CDT 2020: 128.252.0.123: ;; connection timed out; no servers could be reached
```

I write a lot of scripts, probably a few every week.  This is a small example of the scripts stored on one of my laptops.  By looking at their names alone, you will discover the wide variety of uses scripting has.  From basic DNS testing, `whois` lookups, log file processing, [automated wifi testing](/images/wifidongles.jpg), to logging into hundreds of network switches looking for specific settings in order to change change PoE options.  Scripting has no limits.  

```
300license.pl
airport-scan.sh
ap-uptime.pl
apiDev.py
apiInfobloxToFmc.py
aws-boto.py
azure-ping.sh
brownschool_user_stats-JEM.py
buildMailmainAdminList.py
buildWustlFunctions.py
change-uid-gid.sh
check.sh
checkdns.sh
checkdns.sh
chris-pre-ping.sh
chris-up-ping.sh
ciscoWismLogin.py
ciscoWismLogin.py
ciscoWismNameAP.py
ciscoWismNameAP.py
convertVlan.sh
countDhcpUniqueClients.py
cpanel-create-account.sh
createVlan.py
createWirelessNetworksInfobloxCisco-20180929-old-services-chassie.py
createWirelessNetworksInfobloxCisco.py
createWirelessNetworksServiceCisco.py
createWirelessPatDns.py
createwirelessnetworks.py
date.py
debateSwitchTemplate.py
dedup.pl
disable-pmk.py
dns-testing.py
find-host-from-funtion-file.py
find-mac-addresses.py
findAllIpAddresses.py
findFormsWustl.py
findFormsWustl2.py
fix-functions.sh
fix.sh
floorstack.pl
fmcGetNetworkGroups.py
fmcGetRules.py
fmcMaxObjGroupTest.py
fmcMaxObjGroupTestAppend.py
fmcPostNetworkGroups.py
fmcPostPAT.py
fussy_search_test.py
gen-ap-config.pl
gen-cgi-network.pl
gen-cgi-network.py
gen-top-talkers.sh
generateVrfTemplateConfig.py
geoIp.py
geoIp2.py
geoIp3.py
geoIp4.py
getDebateAccounts-BASIC-SINGLE.py
getDebateAccounts.py
getDeviceByIpNetbrain.py
getDhcpStatus.py
getIBXLease.py
getISISNeighbors.py
getMacVendor.py
gettime.py
gronkIP.py
gtt.sh
haversine.py
ibcli.pl
ibx-find-cnames.pl
ibx-get-rndc.pl
ibx-session.pl
ibx-set-leasttime.pl
ibxCleanMacFilter.pl
ibxExtensibleSearch.py
ibxMacFilter.py
ibxMacSearch.sh
infoblox-client-example.py
ip2hostname.pl
ipGeo.py
ironport-blacklist.awk
isInSubnet.py
json-test.py
jsonParse.py
jtemplate.py
juniperNetconfTest.py
lease2sql.py
macConvert.py
meruWirelessDevices.py
mysql-backup.sh
netBrainPathTrace.py
new.py
nexus.py
ngdc-transparent-ping.sh
para.py
parallelProc.py
parse-input.awk
parseInfobloxLog.py
parseJson.py
parseNetBrainHopList.py
parseNetBrainInterfaces.py
parsecsv.py
parsewd.sh
pilog.awk
ping-all.sh
ping-find-up-hosts.sh
pingme.sh
process-wu-monitoried-devices.py
processCiscoRoute.pl
processCiscoRouteFunc.pl
processNexusRoute.pl
pushover.py
removeLeadingZeroFromIp.py
restApi.py
restart-ilo.sh
roundTrip.py
scapy-icmp.py
scapy.py
scrapeWustlForms.py
sendmail.py
setDescCdpNeigh.py
setDescCdpNeigh.py
simplednscheck.sh
snmp-data.py
structureSwitchOuput.py
structuredSwitchOutputExample.py
test-lynn.py
test-wireless.sh
testDns.sh
testTurtle.py
testbro.sh
timeReg.sh
tmux-test-core.sh
traceme.sh
ttest.py
update-controller.py
wcdc-interface-compare.py
wcdc-ping.sh
webHelpDesk.py
webHelpDesk.py
webHelpDeskNewTicket.py
webHelpDeskNewTicket.py
whois.py
wifi-monitor.sh
wuitdc-availability-test.sh
wuitdc-availability-test2.sh
wuitdc-ping-all.sh
wuitdc-ping-nss.sh
wuitdc-ping-small.sh
wuitdc-ping.sh
wustlWirelessDevices.py
xml.py
```

Over the next few days, I will try to pick a few of these scripts and post what they do.  Script week perhaps?
