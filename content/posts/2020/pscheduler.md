---
title: "Using PerfSonar pscheduler to initiate remote speed tests with Iperf3"
tags: [til, perfsonar, pscheduler]
categories:
  - tech
date: 2020-04-21T09:29:03-05:00
---

The [PerfSonar](https://www.perfsonar.net/) `pscheduler` tool can be used to initiate tests, such as iperf3, on remote servers:

```
[jemurray@ps-eps-l29-1 ~]$ pscheduler task --tool iperf3 throughput -d bost-pt1.es.net -t 10
Submitting task...
Task URL:
https://localhost/pscheduler/tasks/0c661c85-fc6c-4065-a300-f2d58b5af123
Running with tool 'iperf3'
Fetching first run...

Next scheduled run:
https://localhost/pscheduler/tasks/0c661c85-fc6c-4065-a300-f2d58b5af123/runs/0e8d3de0-caf2-479d-a43b-1625f3bdcfb9
Starts 2020-04-21T14:35:48Z (~6 seconds)
Ends   2020-04-21T14:36:07Z (~18 seconds)
Waiting for result...

* Stream ID 5
Interval       Throughput     Retransmits    Current Window
0.0 - 1.0      4.89 Gbps      36             67.17 MBytes
1.0 - 2.0      8.35 Gbps      0              67.17 MBytes
2.0 - 3.0      8.44 Gbps      0              67.17 MBytes
3.0 - 4.0      8.41 Gbps      0              67.17 MBytes
4.0 - 5.0      8.59 Gbps      0              67.17 MBytes
5.0 - 6.0      8.47 Gbps      0              67.17 MBytes
6.0 - 7.0      8.66 Gbps      0              67.17 MBytes
7.0 - 8.0      8.60 Gbps      0              67.17 MBytes
8.0 - 9.0      8.47 Gbps      0              67.17 MBytes
9.0 - 10.0     8.57 Gbps      0              67.17 MBytes

Summary
Interval       Throughput     Retransmits
0.0 - 10.0     8.14 Gbps      36

No further runs scheduled.
```
