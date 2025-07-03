---
title: "Using Ansible to manage a network engineers workstation"
date: 2020-05-26T08:18:34-05:00
toc: false
images:
categories:
  - tech
tags: 
  - ansible
  - macOS
  - automation
---

# Overview

I created an Ansible playbook to configure my macOS laptop.  The goal is to create an automation work-flow to consistently (re)build my or another network engineers workstation from scratch.   By using cloud hosted file storage, the primary job of this playbook is to install the proper software and (re)symlink configuration files.

Besides the Google and Box drive tasks, the majority of the configuration should work for almost anyone, see [Post Configuration](#post-configuration) for more details about the cloud file storage systems.


# Prerequisites

Install Ansible: `pip install ansible`

# Create the Playbook

This playbook installs software with HomeBrew and pip, enables the firewall, and creates symlinks to dot files.  It will need to be modified to meet your individual needs.  Pay attention to the use of tags, loops, variables, and conditional statements.

```
- hosts: localhost
  connection: local
  vars:
      brew_packages:
          - python3
          - wget
          - gnupg
          - iperf3
          - nmap
          - mtr
          - sipcalc
          - tmux
          - xz
          - speedtest-cli
          - ssh-copy-id
          - mosh
          - m-cli
          - tree
          - jq
          - socat
          - asciinema
          - htop
          - hugo
      brew_casks:
          - box-drive
          - google-backup-and-sync
          - bettertouchtool
          - evernote
          - backblaze
          - prolific-pl2303
          - postman
          - firefox
          - google-chrome
          - insomnia
      pip3_packages:
          - netmiko
          - boto
          - infoblox-client
          - geoip2
          - Jinja2
          - textfsm
          - maxminddb
          - jupyterlab
          - geoip2

  tasks:
      - name: install homebrew
        shell: 'yes | /usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"'
        args:
          creates: /usr/local/bin/brew
        tags: homebrew

      - name: 'Install Multiple Brew Packages'
        homebrew:
            name: "{{ item }}"
            state: present
        loop: '{{ brew_packages }}'
        tags: homebrew

      - name: 'Install Multiple Brew Casks'
        homebrew_cask:
            name: "{{ item }}"
            state: present
        become: yes
        loop: "{{ brew_casks }}"
        tags: casks

      - name: 'Install pip3 packages'
        pip:
            executable: /usr/local/bin/pip3
            name: "{{ item }}"
        loop: "{{ pip3_packages }}"
        tags: pip

      - name: 'textFSM templates'
        git:
            repo: https://github.com/networktocode/ntc-templates.git
            dest: $HOME/ntc-templates

      - name: 'Enable Firewall'
        shell: m firewall enable
        become: yes

      - name: 'Check to see if Box Drive is installed'
        stat: path='~/Library/Application Support/Box/Box/data/item_status.db'
        register: bdrive_installed
        tags: clouddrive

      - name: 'Check to see if Google Sync is installed'
        stat: path='~/Library/Application Support/Google/Drive/user_default/snapshot.db'
        register: gdrive_installed
        tags: clouddrive

      - name: 'Setup Google Drive Symlinks'
        file:
            src: "$HOME/Google Drive"
            dest: "$HOME/g"
            state: link
        when: gdrive_installed.stat.exists
        tags: clouddrive

      - name: 'Setup Box Drive Symlinks'
        file:
            src: "$HOME/Box"
            dest: "$HOME/b"
            state: link
        when: bdrive_installed.stat.exists
        tags: clouddrive

      - name: 'find dotfiles for symlinks'
        find:
          paths: "$HOME/Google Drive/dotfiles"
          file_type: any
          hidden: yes
        register: findfiles
        when: gdrive_installed.stat.exists
        tags: clouddrive

      - name: 'Create symlinks from dotfiles'
        file:
          src: "{{ item.path }}"
          dest: "$HOME/{{ item.path | basename }}"
          state: link
        with_items: "{{ findfiles.files }}"
        when: gdrive_installed.stat.exists
        tags: clouddrive
```

# Running

## All tasks:
`ansible-playbook build-laptop.yaml --ask-become-pass`

## Specific Tags
`ansible-playbook build-laptop.yaml --ask-become-pass --tags homebrew`

## Available Tags:

* homebrew
* casks
* pip
* clouddrive

# Post Configuration

I use Google Drive for personal files and Box Drive for business file storage.

Both of these systems need to be manually configured after installation.
