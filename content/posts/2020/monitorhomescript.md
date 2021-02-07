---
title: "Script to monitor home Internet service"
date: 2020-07-31T16:11:45-05:00
toc: false
images:
categories:
  - tech
tags: 
  - bash
  - scripting
  - monitoring
---

Over the past few weeks, there has been trouble with our home Internet service.  History proves customer service is better when you come to them with a detailed outage report.  I use a combination of SmokePing for historical graphs and a custom script that runs DNS lookups and ping checks every second.

As part of the 'script week' series, here is a simple `bash` script to monitor home Internet service by performing a DNS lookup to Google servers.  If that fails, it starts a timer and then checks to see if the immediate gateway router is available.   This helps me determine if the issue is local to my area or something deeper in the cable providers network.   I log all this information to a file which is easily referenced when calling for support.


The script:

```
#!/bin/bash

starttime=0
endtime=0

while [ 1 ]; do
        result=`host google.com 8.8.8.8 > /dev/null 2&>1`
        if [ $? -ne 0 ]; then
                echo "`date` Internet down"
                if [ $starttime -eq 0 ]; then
                        starttime=`date +%s`
                        # Get the gateway IP from Google WiFi router
                        gatewayip=`curl -s http://192.168.86.1/api/v1/status | jq -r '.wan.gatewayIpAddress'`
                fi

                result=`ping -c 1 $gatewayip`
                if [ $? -ne 0 ]; then
                        echo "Gateway: $gatewayip is down"
                else
                        echo "Gateway: $gatewayip is OK"
                fi

        else
                echo "`date` Internet OK"
                if [ $starttime -ne 0 ]; then
                        endtime=`date +%s`
                        totaltime=`expr $endtime - $starttime`
                        echo "Outage over at `date` for seconds: $totaltime"
                        starttime=0
                fi
        fi
        sleep 1
done
```

The script output is logged to a file by running:

```
./ping-test-home-network.sh | tee -a ./logfile.txt
```

The output looks like this when service is down:

```
Fri Jul 31 01:02:52 CDT 2020 Internet down
Fri Jul 31 01:03:03 CDT 2020 Internet down
Fri Jul 31 01:03:14 CDT 2020 Internet down
Fri Jul 31 01:03:25 CDT 2020 Internet down
Fri Jul 31 01:03:36 CDT 2020 Internet down
Fri Jul 31 01:03:47 CDT 2020 Internet down
Fri Jul 31 01:03:58 CDT 2020 Internet down
Fri Jul 31 01:04:09 CDT 2020 Internet down
Fri Jul 31 01:04:20 CDT 2020 Internet down
Fri Jul 31 01:04:31 CDT 2020 Internet down
Fri Jul 31 01:04:42 CDT 2020 Internet down
Fri Jul 31 01:04:53 CDT 2020 Internet down
Fri Jul 31 01:05:04 CDT 2020 Internet down
Fri Jul 31 01:05:15 CDT 2020 Internet down
Fri Jul 31 01:05:26 CDT 2020 Internet down
Fri Jul 31 01:05:37 CDT 2020 Internet down
Fri Jul 31 01:05:48 CDT 2020 Internet down
Fri Jul 31 01:05:59 CDT 2020 Internet down
Fri Jul 31 01:06:10 CDT 2020 Internet down
Fri Jul 31 01:06:21 CDT 2020 Internet down
Fri Jul 31 01:06:32 CDT 2020 Internet down
Fri Jul 31 01:06:43 CDT 2020 Internet down
Outage over at Fri Jul 31 01:06:44 CDT 2020 for seconds: 232
```