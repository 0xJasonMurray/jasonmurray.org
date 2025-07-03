---
title: "Python Script to dump all devices from NetBrain"
date: 2020-07-16T09:17:00-05:00
toc: false
images:
categories:
  - tech
tags: 
  - netbrain
  - api
  - python
---

The NetBrain API requires a few steps before getting at the data:

- [Login](https://github.com/NetBrainAPI/NetBrain-REST-API-V8.01/blob/master/REST%20APIs%20Documentation/Authentication%20and%20Authorization/Login%20API.md) and retrieve the token for API calls
- Set the [domain and tenant](https://github.com/NetBrainAPI/NetBrain-REST-API-V8.01/blob/master/REST%20APIs%20Documentation/Authentication%20and%20Authorization/Specify%20A%20Working%20Domain%20API.md) where the data is stored
- Make the API calls to do work
- [Logout](https://github.com/NetBrainAPI/NetBrain-REST-API-V8.01/blob/master/REST%20APIs%20Documentation/Authentication%20and%20Authorization/Logout%20API.md) so you don't take up login sessions

The [`CMDB/Devices`](https://github.com/NetBrainAPI/NetBrain-REST-API-V8.01/blob/master/REST%20APIs%20Documentation/Devices%20Management/Get%20Device%20API%20Version_1.md) API call only returns [100](https://github.com/NetBrainAPI/NetBrain-REST-API-V8.01/blob/master/REST%20APIs%20Documentation/Devices%20Management/Get%20Device%20API%20Version_1.md#query-parameters-required) devices at a time.  There is logic in this script to deal with that shortcoming.  

All of the output is for debugging purposes only.  They key takeaways from this post is to show the login / logout methods and how to dump a CMDB that has more then 100 entries in it.

Full example of login, setting the domain/tenant, dumping the CMDB, and logging out:

```
#!/usr/bin/env python3

# import python modules
import requests
import time
import urllib3
import pprint
#urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
import json


### Global Variables
# Base URL for API
nb_url = "https://netbrain.example.com"
# Site Specific
tenant = "YOUR_TENANT_ID"
domain = "YOUR_DOMAIN_ID"
# Set headers
headers = {'Content-Type': 'application/json', 'Accept': 'application/json'}
# Set authentication information
username = "YOUR_USERNAME"
passwd = "YOUR_PASSWORD"
authid = "Tacacs"


def login(nb_url, headers):
    body = {
        "username" : username,
        "password" : passwd,
        "authentication_id" : authid
    }

    # Set the requests input URL
    full_url = nb_url + "/ServicesAPI/API/V1/Session"

    try:
        # Do the HTTP request
        response = requests.post(full_url, headers=headers, data = json.dumps(body), verify=True)
        # Check for HTTP codes other than 200
        if response.status_code == 200:
            # Decode the JSON response into a dictionary and use the data
            js = response.json()
            print (js)
            return js["token"]
        else:
            print ("Get token failed! - " + str(response.text))
    except Exception as e:
        print (str(e))


def set_domain(nb_url, headers, tenant, domain):
    # Set the request inputs URL
    full_url = nb_url + "/ServicesAPI/API/V1/Session/CurrentDomain"

    # Set proper headers
    body = {
            "tenantId": tenant,
            "domainId": domain
        }

    try:
        # Do the HTTP request
        response = requests.put(full_url, data=json.dumps(body), headers=headers, verify=True)
        # Check for HTTP codes other than 200
        if response.status_code == 200:
            # Decode the JSON response into a dictionary and use the data
            result = response.json()
            print (result)
        elif response.status_code != 200:
            print ("Login failed! - " + str(response.text))

    except Exception as e: print (str(e))



def get_all_devices(nb_url, headers):

    # Used for debugging the JSON output
    pp = pprint.PrettyPrinter(indent=4)

    # Set the requests input URL
    full_url = nb_url + "/ServicesAPI/API/V1/CMDB/Devices"

    # API only returns 100 entries at a time.  Variables to loop through
    # and return all entries
    limit = 100
    totaldevices = limit
    grandtotal = 0
    skip = 0

    # API only returns 100 entries.  Logic to get all data.
    while totaldevices >= limit:
        data = {
            "version": 1,
            "limit": limit,
            "skip": skip
        }

        try:
            response = requests.get(full_url, params = data, headers = headers, verify = True)
            if response.status_code == 200:
                result = response.json()
                # Debugging output
                pp.pprint(result)
                totaldevices = len(result["devices"])
                grandtotal = grandtotal + totaldevices
                print("Total Devices: " + str(totaldevices))
                print("Grand Total Devices: " + str(grandtotal))
                skip = skip + totaldevices
            else:
                print("Get Devices failed! - " + str(response.text))
        except Exception as e:
            print (str(e))


def logout(nb_url, headers):
    # Set the request inputs
    full_url = nb_url + "/ServicesAPI/API/V1/Session"

    try:
        # Do the HTTP request
        response = requests.delete(full_url, headers=headers, verify=True)
        # Check for HTTP codes other than 200
        if response.status_code == 200:
            # Decode the JSON response into a dictionary and use the data
            js = response.json()
            print (js)
        else:
            print ("Session logout failed! - " + str(response.text))

    except Exception as e:
        print (str(e))


if __name__ == "__main__":
    token = login(nb_url, headers)
    headers["Token"]=token
    set_domain(nb_url, headers, tenant, domain)
    get_all_devices(nb_url, headers)
    logout(nb_url, headers)
```