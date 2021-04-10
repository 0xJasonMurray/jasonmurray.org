---
title: "Internet2 Technology Exchange: WashU MPLS Presentation"
date: 2019-12-12T09:30:06-05:00
toc: false
images:
categories:
  - tech
tags: 
  - mpls
  - presentations
  - networking
  - speaking
  - cisco
  - juniper
  - arista
  - zeek
  - vrf
  - nexus
description: A journey through the Washington University in St. Louis "One Campus Project", the birth of our MPLS network, and the adoption and adaptation of security controls from the National Institute of Standards and Technology (NIST)
---

On 2019-12-12 [I presented](https://meetings.internet2.edu/2019-technology-exchange/speakers/6642/) on the Washington University in St. Louis One Campus enterprise MPLS design at the [Internet2 Tech-Exchange](https://meetings.internet2.edu/2019-technology-exchange/).

## SESSION ABSTRACT

### OVERVIEW

A journey through the Washington University in St. Louis "One Campus Project", the birth of our MPLS network, and the adoption and adaptation of security controls from the National Institute of Standards and Technology (NIST).

### Problem Statement

The technology landscape across the university has been changing at a rapid pace. Significant events, such as the consolidation of the Medical School Central Information Technology (MSCIT) and the academic campus Information Services and Technology into the single organization known as WashU IT and the reorganization of operational and help desk entities, have caused strain on the simplicity, scalability, reliability, security, and operational support of the core networking infrastructure. The main source of problems stem from the legacy environments that continue to be operated as if they were independent entities, with their own policy, procedures, tools, and configurations standards. This lack of vision requires our engineering and operation staff to continually architect ad-hoc solutions to bridge these environments together in ways that were never intended by their original designers.

### One Campus Purpose

One Campus is a project started by network engineering to reevaluate and redesign the way networking services are delivered to Washington University in St. Louis. The One Campus project focuses on the following key tenants:


- Reevaluate - Meet with WashU members to understand their needs and the mission of the university. Revisit all parts of the network to determine what is working and what is not based on this feedback.
- Unified Campus - Design, configure, and deploy a unified campus infrastructure. All components of the network should be designed in a unified way based on standard templates and design best practices.
- Simplified Services - Concentrate on delivery a core set of services really well.
- Eliminate complex one-off solutions.
- One User Experience - Users are mobile now more than ever. Deliver the same network service no matter the users location.
- Security - Data is the foundation of the university, it must be protected.
- Automation - Reduce manual operations in order to decrease human errors, speed up service delivery, and standardize on configuration.
- Reliability - A common unified platform will reduce operational complexities, standardize monitoring and troubleshooting which will result in a stable, predictable, and reliable network.

### Technologies Covered

- Multi-protocol Label Switching (MPLS) deployments using the Label Distribution Protocol (LDP), Intermediate System to Intermediate System (IS-IS) routing protocol, and Border Gateway Protocol (BGP) on Cisco Catalyst 3K and 9K switches, Cisco Nexus switches, and Juniper MX series routers.
- Secure fusion routing between virtual routing and forwarding (VRF) tables through a transparent cluster/pair of Cisco FirePOWER firewalls
- Information Security (InfoSec) data collection using a Zeek (formerly Bro) worker cluster that's fed through in-line optical taps and a 40GE Arista "Tap Agg" network.