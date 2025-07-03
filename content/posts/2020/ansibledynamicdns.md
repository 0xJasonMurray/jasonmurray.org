---
title: "Using Ansible as a dynamic DNS update client"
date: 2020-05-21T16:04:59-05:00
toc: false
images:
categories:
  - tech
tags: 
  - til
  - ansible
  - aws
  - route53
---

## Overview

Here is an Ansible playbook to update dynamic DNS entires using AWS Route 53, which can be used as an alternative to dynamic DNS services such as afraid.org, noip.com, etc.


## Configuration

### Requirements

Install boto python library:

```
pip3 install boto
```

### AWS Authentication and Authorization

Create the following environment variables by adding to your local shell configuration file such as the $HOME/.bashrc

```
AWS_ACCESS_KEY=<key goes here>
AWS_SECRET_KEY=<secret goes here>
```

### Inventory file

Create an Ansible inventory file:

```
[local]
localhost   ansible_python_interpreter=/usr/local/bin/python3
```

### The Script

Create the Ansible script:

```
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
      zone: example.com
      record: home.example.com

  tasks:
      - name: Get Dynamic IP address
        uri:
            url: https://api.ipify.org/?format=json
            return_contents: yes
        register: json_response

      - name: Update Dynamic DNS in AWS
        route53:
            state: present
            zone: "{{ zone }}"
            record: "{{ record }}"
            type: A
            ttl: 300
            value: "{{ json_response.json.ip }}"
            overwrite: yes
            wait: yes

      - name: debug
        debug:
            var: json_response.json.ip
```

### Executing

Run the playbook:

```
ansible-playbook -i hosts dynamic-aws.yaml
```

[GitHub Repo](https://github.com/0xJasonMurray/aws-ansible-dynamic-dns)


