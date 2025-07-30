---
title: Default Quotas
lang: "en"
permalink: /openstack/specs/default_quota/
parent: Specifications
nav_order: 9200
last_modified_date: 2025-07-21
---

OpenStack Default Quotas
========================

In the OpenStack-Platform we have defined default quotas for the OpenStack Compute service, the OpenStack Block Storage service, and the OpenStack Networking service. We also have separate quotas for the Octavia Loadbalancer service and its associated components. These default values for new projects are listed below, if you need some quota to be increased please open a ticket for it via [helpdesk.de@wiit.one](mailto:helpdesk.de@wiit.one)

Compute
----------------

|**Field**                 |**Value**            |
|:-------------------------|:--------------------|
| vCPU                     |        256          |
| pCPU                     |        256          |
| Injected File Size       |        10240        |
| Injected Files           |        100          |
| Instances                |        100          |
| Key Pairs                |        100          |
| Properties               |        128          |
| Ram                      |        524288       |
| Server Groups            |        10           |
| Server Group Members     |        10           |

Block Storage
----------------------

|**Field**                 |**Value**            |
|:-------------------------|:--------------------|
| Backups                  |        100          |
| Backup Gigabytes         |        10000        |
| Gigabytes                |        10000        |
| Per-volume-gigabytes     |        Unlimited    |
| Snapshots                |        100          |
| Volumes                  |        100          |

Network
----------------

|**Field**                 |**Value**            |
|:-------------------------|:--------------------|
| Floating IPs             |        15           |
| Secgroup Rules           |        1000         |
| Secgroups                |        100          |
| Networks                 |        100          |
| Subnets                  |        200          |
| Ports                    |        500          |
| Routers                  |        50           |
| RBAC Policies            |        100          |
| Subnetpools              |        Unlimited    |

Octavia Loadbalancers
----------------

|**Field**                 |**Value**            |
|:-------------------------|:--------------------|
| Load Balancers           | 50                  |
| Listeners                | 200                 |
| Pools                    | 200                 |
| Health Monitors          | 10000               |
| Members                  | 10000               |
