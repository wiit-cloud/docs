---
title: Default Quotas
lang: "de"
permalink: /openstack/specs/default_quota/
parent: Spezifikationen
nav_order: 9200
last_modified_date: 2025-07-21
---

OpenStack Default Quotas
========================

Im der Openstack Plattform haben wir Standardwerte für den OpenStack Compute-Dienst, den OpenStack Block Storage-Dienst und den OpenStack Networking-Dienst definiert. Wir haben auch separate Quotas für den Octavia Loadbalancer-Dienst und die zugehörigen Komponenten. Diese Standardwerte für neu erstellte Projekte sind unten aufgeführt, sollten sie eine Anpassung benötigen erstellen sie bitte ein Ticket über unseren Helpdesk: [helpdesk.de@wiit.one](mailto:helpdesk.de@wiit.one)

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
