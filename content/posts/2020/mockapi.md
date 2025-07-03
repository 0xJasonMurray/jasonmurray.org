---
title: "Creating a mock API endpoint for testing"
date: 2020-07-29T11:05:01-05:00
toc: false
images:
categories:
  - tech
tags: 
  - python
  - flask
  - api
---

[NetBrain](https://www.netbraintech.com/) is a tool we use at work for CMDB, documentation, and network troubleshooting.  NetBrain exposes an [API](https://github.com/NetBrainAPI) which gives tools like [Nornir](https://github.com/nornir-automation/nornir) access to dynamically generated inventory and group objects.   However the modules, don't exist yet; [I am writing one](https://nornir.readthedocs.io/en/latest/howto/writing_a_custom_inventory_plugin.html).  


The issue is the NetBrain system sits on an access restricted network in our remote data center, and I want to write code at home without being on VPN.  By using Python and Flask, I was able to write a mock NetBrain API, that simulates connecting to the remote server.  This approach has the added benefit of not using any resources on the production server while I work on the code and perform debugging.   

By changing the Flask route decorator with the path to the API resource and returning the appropriate information, this concept can be extended to any project where mock API access is needed.  For example, the login API: `/ServicesAPI/API/V1/Session` does not have to perform any "real" login functions.  The data passed into the API is accepted, but ignored.  What is important is the API returns the same data structure as the real system, in this case a `token` object with a value.  None of the data has to be accurate, it only has to contain something to pass into the next function.

Code to simulate NetBrain API:

{{< highlight python  "linenos=table" >}}
#!/usr/bin/env python3

from flask import Flask
app = Flask(__name__)

# Login
@app.route('/ServicesAPI/API/V1/Session', methods=['POST'])
def login():
    output = {'token': 'this-is-a-mock-token'}
    return output

@app.route('/ServicesAPI/API/V1/Session/CurrentDomain', methods=["PUT"])
def set_domain():
    output = {'domain': 'Domain Set'}
    return output


@app.route('/ServicesAPI/API/V1/CMDB/Devices', methods=['GET', 'POST'])
def dump_data():
    output = {'devices': [{'name': 'router1', 'mgmtIP': '192.168.1.10'}, {'name': 'router22, 'mgmtIP': '192.168.1.12'}]}
    return output


@app.route('/ServicesAPI/API/V1/Session', methods=['DELETE'])
def logout():
    output = {'logout': 'OK'}
    return output
{{< / highlight >}}

Run the Flask app:

```
export FLASK_APP=./mock-api.py
flash run
```
