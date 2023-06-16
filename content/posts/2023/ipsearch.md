---
title: "Using iplocate to geolocate IP addresses in Splunk."
date: 2023-06-15T21:55:41-05:00
toc: false
images:
categories:
  - tech
tags: 
  - splunk
  - iplocate
description: Using iplocate to geolocate IP addresses in Splunk.
---

# Overview

Converting an IP address into a physical location can greatly enhance an Incident Responder's decision-making process. It enables them to identify patterns or eliminate irrelevant data by focusing on specific countries. With the use of the `|iplocate` command in Splunk, what was once considered a complex and specialized skill becomes a straightforward task.

# Technical Details

In this example, the Zeek connection data is enhanced by including the geolocated source IP address for each connection.

To begin, we will use a simple Zeek conn_log query that focuses on:

- Identifying connections made to a specific host
- Filtering out external IP addresses
- Selecting connections that involved a download of more than 5K bytes

```
index=zeek sourcetype=zeek_conn id.resp_h=192.168.86.100 AND (id.resp_p=80 OR id.resp_p=443) AND resp_bytes>5000 AND (id.orig_h!=10.0.0.0/8) 
| table _time, id.orig_h, id.resp_p, conn_state, duration, resp_shunted_bytes, resp_ip_bytes, resp_bytes 
| sort -resp_bytes
```

Which outputs:

```
_time	id.orig_h	id.resp_p	conn_state	duration	resp_shunted_bytes	resp_ip_bytes	resp_bytes
2023-06-15 14:22:44.455	151.73.38.94	80	SF	10.873228073120117	 	3274	4294968721
2023-06-15 06:14:02.512	120.86.253.36	80	SF	19.41591215133667	 	3316	4294968702
2023-06-15 18:25:39.162	52.114.128.147	80	SF	0.04450201988220215	 	2038	4294968103
2023-06-15 07:03:25.599	61.61.179.143	80	SF	0.23454689979553223	 	1318	4294967823
2023-06-15 13:08:06.943	3.235.31.29	80	SF	0.05164289474487305	 	1274	4294967801
2023-06-15 11:48:34.510	176.194.53.33	80	SF	20.484610080718994	 	1258	4294967713
...
```

Now, let's enrich the source IP address (id.orig_h) with the City and Country it originated from, then use this information to exclude everything from the United States:

```
index=zeek sourcetype=zeek_conn id.resp_h=192.168.86.100 AND (id.resp_p=80 OR id.resp_p=443) AND resp_bytes>5000 AND (id.orig_h!=10.0.0.0/8) 
| iplocation id.orig_h 
| where Country!="United States"
| table _time, id.orig_h, City, Country,  id.resp_p, conn_state, duration, resp_shunted_bytes, resp_ip_bytes, resp_bytes
| sort -resp_bytes
```

Which outputs:

```
_time	id.orig_h	City	Country	id.resp_p	conn_state	duration	resp_shunted_bytes	resp_ip_bytes	resp_bytes
2023-06-15 14:22:44.455	151.73.38.94	Rho	Italy	80	SF	10.873228073120117	 	3274	4294968721
2023-06-15 06:14:02.512	120.86.253.36	Shenzhen	China	80	SF	19.41591215133667	 	3316	4294968702
2023-06-15 07:03:25.599	61.61.179.143	Tuniugou	Taiwan	80	SF	0.23454689979553223	 	1318	4294967823
2023-06-15 11:48:34.510	176.194.53.33	Moscow	Russia	80	SF	20.484610080718994	 	1258	4294967713
2023-06-15 20:57:27.714	119.224.103.159	Auckland (Auckland CBD)	New Zealand	443	SF	0.39174818992614746	 	654	4294967303
2023-06-15 20:57:26.480	119.224.103.159	Auckland (Auckland CBD)	New Zealand	443	SF	0.41156911849975586	 	550	4294967303
2023-06-15 22:12:57.538	194.165.16.78	Vilnius	Lithuania	443	RSTO	0.2701132297515869	 	264	4294967296
2023-06-15 05:20:56.761	36.88.180.115	Jakarta	Indonesia	80	SF	24.700366020202637	 	629	2107393772
2023-06-15 07:33:32.710	94.189.166.118	Belgrade	Serbia	443	RSTO	14.081745862960815	 	2348967	1892331044
2023-06-15 07:32:19.574	94.189.166.118	Belgrade	Serbia	443	RSTO	39.91005301475525	 	2616378	1581744615
...
```

