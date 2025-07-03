---
title: "How to test if a JSON key exists with Python"
date: 2020-07-17T09:36:07-05:00
toc: false
images:
categories:
  - tech
tags: 
  - python
  - json
---

Using an `if` statement to test if a JSON key exists in Python results in the following error:

```
Traceback (most recent call last):
  File "/Users/jemurray/Documents/src/personalPython/jsontest.py", line 56, in <module>
    if i["keyDoesNotExist"]:
KeyError: 'keyDoesNotExist'
```

Instead, use the `in` statement to test if the JSON key exists:

```
# Looking for a JSON key using the `in` statement
if "keyDoesNotExist" not in i:
    print("Key not found, no errors")
```

Here is an example using `if` and `in` statements and the output they return:

{{< highlight python  "linenos=table" >}}
#!/usr/bin/env python3

jsondata = {   'devices': [   { 
                       'driverName': 'test data',
                       'fDiscoveryTime': 'test data',
                       'hasBGPConfig': 'test data',
                       'hasEIGRPConfig': 'test data',
                       'vendor': 'test data',
                       'ver': 'test data'},
                   {   'driverName': 'test data',
                       'fDiscoveryTime': 'test data',
                       'hasBGPConfig': 'test data',
                       'hasEIGRPConfig': 'test data',
                       'hasISISConfig': 'test data',
                       'vendor': 'test data',
                       'ver': 'test data'} ] }

for i in jsondata["devices"]:
    # Looking for a key using the `if` statement works if the key exists:
    if i['driverName']:
        print("Key Exists: " + i["driverName"])

    # Looking for a key using the `in` statement works without error:
    if "keyDoesNotExist" not in i:
        print("Key not found, no errors")

    # Looking for a key that does not exist with an `if` statement results in KeyError and program exit:
    if i["keyDoesNotExist"]:
        print("Broken")
{{< / highlight >}}

The output (commented for clarity):

```
jemurray@mbp-2019:~/Documents/src/personalPython $ ./jsontest.py

# Using 'if' when the key exits:
Key Exists: test data

# Using 'in' when the key does not exist:
Key not found, no errors

# Using 'if' when the key does not exist:
Traceback (most recent call last):
  File "/Users/jemurray/Documents/src/personalPython/jsontest.py", line 28, in <module>
    if i["keyDoesNotExist"]:
KeyError: 'keyDoesNotExist'
```