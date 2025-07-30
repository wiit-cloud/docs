---
title: Intro  
lang: "en"  
permalink: /openstack/intro/  
nav_order: 998
---

# Intro

## de-west-01 Region

WIIT AG's OpenStack platform provides infrastructure components in a self-service model as Infrastructure as a Service (IaaS).  
Resources can be managed either via the OpenStack APIs or through the [OpenStack Dashboard](https://openstack.wiit-cloud.io/).

Currently, the platform is available in the "de-west-01" region (location: Düsseldorf) with the following services:

### Region Architecture  
![](attachments/wcnp_region_architecture.png)

### Availability Zones

The OpenStack services in the "de-west-01" region are distributed across three data centers.  
Horizon, Keystone, Glance, Neutron, Octavia, Designate, and Object Storage are operated by WIIT AG across all sites.  
For Nova and Cinder, the data centers are represented as availability zones (AZ1, AZ2, AZ3) within OpenStack. This allows resources to be deliberately distributed across physical sites, aiming for optimal fault tolerance.

### Stretched Zone (SZ1)

In addition to the physical availability zones, WIIT AG also provides a “Stretched Zone” (SZ1) spanning all data center sites within the region.  
This zone is also selectable as an availability zone in OpenStack. Compute resources launched in SZ1 are automatically distributed across hosts at all three locations.  
If an entire physical zone fails, these instances can be restarted on another host within SZ1.

Block storage volumes are also available in SZ1. Data stored on volumes in SZ1 is redundantly distributed across all three data centers.

**Important:** Volumes can only be attached to instances in the same availability zone — this also applies to SZ1. Therefore, volumes in SZ1 can only be used with instances in SZ1.

### Services & APIs

| **Service**                   | **API Endpoint**                                                                                                             |
|:------------------------------|:-----------------------------------------------------------------------------------------------------------------------------|
| Horizon (Dashboard)           | [https://openstack.wiit-cloud.io/](https://openstack.wiit-cloud.io/)                                                         |
| Keystone (Identity)           | [https://identity.openstack.de-west-01.wiit-cloud.io](https://identity.openstack.de-west-01.wiit-cloud.io)                   |
| Glance (Image Service)        | [https://image.openstack.de-west-01.wiit-cloud.io](https://image.openstack.de-west-01.wiit-cloud.io)                         |
| Nova (Compute Service)        | [https://compute.openstack.de-west-01.wiit-cloud.io](https://compute.openstack.de-west-01.wiit-cloud.io)                     |
| Neutron (Network Service)     | [https://network.openstack.de-west-01.wiit-cloud.io](https://network.openstack.de-west-01.wiit-cloud.io)                     |
| Cinder (Block Storage)        | [https://volume.openstack.de-west-01.wiit-cloud.io](https://volume.openstack.de-west-01.wiit-cloud.io)                       |
| Octavia (Load Balancer)       | [https://load-balancer.openstack.de-west-01.wiit-cloud.io](https://load-balancer.openstack.de-west-01.wiit-cloud.io)         |
| Designate (DNS Service)       | [https://dns.openstack.de-west-01.wiit-cloud.io](https://dns.openstack.de-west-01.wiit-cloud.io)                             |
| Object Storage / Swift        | [https://s3.openstack.de-west-01.wiit-cloud.io/swift](https://s3.openstack.de-west-01.wiit-cloud.io/swift/)                  |
| Object Storage / S3           | [https://s3.openstack.de-west-01.wiit-cloud.io](https://s3.openstack.de-west-01.wiit-cloud.io)                               |

## Service Status

For transparent and up-to-date information regarding maintenance, limitations, or incidents, please refer to our [Statuspage](https://status.wiit-cloud.io).

The following Services on the Statuspage are relevant for the OpenStack-Platform:  

- **de-west-01 OpenStack Services:** Availability of the individual Services
- **de-west-01 OpenStack APIs:** Availability of OpenStack APIs
- **de-west-01 OpenStack AZ1:** Availability of Compute, Storage and Network in AZ1
- **de-west-01 OpenStack AZ2:** Availability of Compute, Storage and Network in AZ2
- **de-west-01 OpenStack AZ3:** Availability of Compute, Storage and Network in AZ3
- **de-west-01 OpenStack SZ1:** Availability of Compute, Storage and Network in SZ1

## Maintenance

We announce planned maintenance work on the [Statuspage](https://status.wiit-cloud.io) in advance, especially if service limitations may occur.  
During maintenance, short interruptions of API access may happen – however, the functionality of existing resources remains unaffected (Loss of Control).

Maintenance on zone-specific services (Compute/Block Storage) is carried out sequentially per AZ.  
As part of Compute maintenance, instances may need to be migrated between Compute Nodes. These necessary "Live Migrations" can, in some cases, cause brief interruptions to the affected instances.

## Support

If you have questions or encounter issues when using the OpenStack platform, please contact our support team: [helpdesk.de@wiit.one](mailto:helpdesk.de@wiit.one)

To avoid back-and-forth and ensure quick processing, please use the following template in your request:

```
### OpenStack Issue Report

**Affected OpenStack Components:**  
(e.g. Glance, Nova, Neutron, ...)

**Project ID / Domain ID:**  
project_id:  
domain_id:  

**Problem Description:**  
(What happened?)

**Expected Behavior:**  
(What should have happened?)

**Steps to Reproduce:**  
1.  
2.  
3.  

**Actual Result / Error Output:**  
(Logs, error codes, tracebacks...)

**Timestamp and Region (if applicable):**  
(e.g. 2025-07-18 13:42 CEST / Region: de-west-01 / FRA?)

**Affected Resource IDs (optional):**  
(server_id=..., volume_id=...)

**Were any workarounds or tests attempted?**  
- [ ] Yes (please describe below)  
- [ ] No

**Priority:**  
- [ ] Critical (Production impact)
- [ ] High
- [ ] Medium
- [ ] Low

**Attachments:**
(Logs, screenshots, `openstack --debug` output, API dumps...)

```
