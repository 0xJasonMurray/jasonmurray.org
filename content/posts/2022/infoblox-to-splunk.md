---
title: "Export Infoblox IPv4 Networks into Splunk Lookup Table"
date: 2022-09-05T16:34:41-05:00
toc: false
images:
categories:
  - tech
tags: 
  - infoblox
  - splunk
  - python
  - inputlookup
  - csv
  - cidr
description: How to export all IPv4 networks from Infoblox, convert the address/netmask to CIDR using Python, import the data into a Splunk lookup table, then search for matching CIDR's.
---

I want to search for Infoblox subnets in Splunk using lookup tables like this:

```text
|inputlookup infoblox-ipv4-network-export.csv | search | where cidrmatch(CIDR,"128.252.120.128")  | table CIDR, address*, netmask*, EA-School, comment, domain_name
```

What follows are the steps to export the data from Infoblox, convert the address and netmask to a CIDR block, import the data into a Splunk lookup table, then search the data using CIDR matching syntax.

## Technical Details

- First, export the data from Infoblox using the Global CSV export console (Data Management -> IPAM -> CSV Job Manager -> CSV Export -> +):

[![Image of new csv export job](/images/2022-09-05-16-45-09.png)](/images/2022-09-05-16-45-09.png)

- Uncheck all the export objects and select `IPv4 Network` only: 

[![Image of global csv export wizard](/images/2022-09-05-16-46-23.png)](/images/2022-09-05-16-46-23.png)

- Click `Export Data`. Depending on the size it will take a while to export.

- When the export job is complete, click the hamburger menu and select `Download file`: 

[![Image of csv job manager to download the file ](/images/2022-09-05-16-49-53.png)](/images/2022-09-05-16-49-53.png)

- The CSV export writes networks in `address / netmask` format, it does not contain CIDR addresses. To use the `cidrmatch()` function in Splunk, we must add a CIDR address. Use this Python script to compute a /CIDR and add an additional column to each row with the new value:

  ```python
  #!/usr/bin/env python3

  import csv
  from ipaddress import IPv4Network

  with open('infoblox-ipv4-network-export.csv', 'r') as fin:
      reader = csv.reader(fin, delimiter=',')
      with open('new_infoblox-ipv4-network-export.csv', 'w') as fout:
          writer = csv.writer(fout, delimiter=',')
          # set headers here, grabbing headers from reader first
          writer.writerow(next(reader) + ['CIDR'])

          for row in reader:
              address = row[1]
              network = row[2]
              cidr = IPv4Network(address + '/' + network).prefixlen
              row.append(address + '/' + str(cidr))
              writer.writerow(row)
    ```

- Import the CSV data into a Splunk lookup table (Settings -> Lookups -> Lookup table files -> + Add New):

[![Image of import csv data into a splunk lookup table](/images/2022-09-05-17-00-40.png)](/images/2022-09-05-17-00-40.png)

- Use Splunk to query the data using a CIDR match:

```
|inputlookup infoblox-ipv4-network-export.csv | search | where cidrmatch(CIDR,"128.252.120.128")  | table CIDR, address*, netmask*, EA-School, comment, domain_name
```

[![Image of output of splunk lookup table search](/images/2022-09-05-17-02-31.png)](/images/2022-09-05-17-02-31.png)

