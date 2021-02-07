---
title: "Adding host records to Infoblox with Ansible"
date: 2020-09-25T13:28:48-05:00
toc: false
images:
categories:
  - tech
tags: 
  - infoblox
  - ansible
  - hostrecords
---

Entering tens or hundreds of DNS entries in Infoblox from the GUI is a slow process.  Previously, I used the [IBCLI](https://github.com/slchorne/ibcli) which is showing its age.  I am concerned future updates may make it obsolete at some point.

A more modern approach is a configuration tool like Ansible or the restAPI.  

The following playbook has an array of key/value pairs which contain hostname and IP information for the `host_record` task.  It is much easier to update a text file with the basic key/value pairs then clicking several times for each host record in the GUI.

Ansible playbook:

```
---
- hosts: localhost
  connection: local
  vars:
    local_nios_provider:
      host: grid.example.com
      username: jemurray
      password: "your-password"
    host_records:
      - { host_name: test1.example.com, ipaddr: 127.0.0.1 }
      - { host_name: test2.example.com, ipaddr: 127.0.0.1 }
      - { host_name: test3.example.com, ipaddr: 127.0.0.1 }

  tasks:
    - name: Create host record
      nios_host_record:
        name: "{{ item.host_name }}"
        ipv4addrs:
          - ipv4addr: "{{ item.ipaddr }}"
        state: present
        provider: "{{ local_nios_provider }}"
      loop: "{{ host_records }}"
```

Running the playbook:

```
(.venv) jemurray@mbp-2019:~/Documents/src/ansible/tasks/infoblox $ ansible-playbook ./add-host-record.yaml

PLAY [localhost] **********************************************************************************************************************************************************************

TASK [Gathering Facts] ****************************************************************************************************************************************************************
ok: [localhost]

TASK [Create host record] *************************************************************************************************************************************************************
changed: [localhost] => (item={'host_name': 'test1.example.com', 'ipaddr': '127.0.0.1'})
changed: [localhost] => (item={'host_name': 'test1.example.com', 'ipaddr': '127.0.0.1'})

PLAY RECAP ****************************************************************************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

