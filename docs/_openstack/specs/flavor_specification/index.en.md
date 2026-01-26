---
title: Flavor Specifications
lang: "en"
permalink: /openstack/specs/flavor_specification/
parent: Specifications
nav_order: 9100
last_modified_date: 2025-07-21
---

# Flavor Specifications

In the OpenStack context the term "flavor" refers to a hardware profile that can be used for a virtual machine. In openstack we have set up
various standard hardware profiles (flavors). These have different limits, which are listed below for all available flavors.

## Migrating between Flavor Types

To change the flavors of existing instances, the OpenStack ["Resize Instance"](/openstack/faq/#how-can-i-change-the-flavor-of-a-virtual-machine-instance-resize) Option can be used either via the Dashboard or the CLI. This will result in a reboot of the Instance but the content of the instance will be preserved. Please note that changing Flavors from Large Root Disk Types to a Flavor with a smaller Root Disk is not possible.

## Flavor-Types

- s1 (Standard) Flavors: shared CPU, 1:2 CPU / memory ratio
- m1 (Memory-Optimized) Flavors: shared CPU, 1:4 CPU / memory ratio
- d1 (Dedicated) Flavors: pinned CPU, 1:8 CPU / memory ratio
- g1 (GPU) Flavors: pinned CPU, A100 80GB GPU(s) - only available in SZ1 and no Migration Support


All default flavors are available in all availability zones as well as in SZ1 by default, unless specified otherwise.
In addition to the standard variant with a 20GB root disk, we offer a .d variant with a 100GB root disk.
Alternatively, any flavor can used with a volume as the root disk.

### s1 (Standard) Flavors

| Name | Cores |   RAM |  Disk | IOPS Limits (read/write) | IO throughput rate (read/write) |
| :---------- | ----: | ----: | ----: | -----------------------: | ------------------------------: |
| s1.micro    |     1 |  2 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.small    |     2 |  4 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.medium   |     4 |  8 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.large    |     8 | 16 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.xlarge   |    16 | 32 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.2xlarge  |    32 | 64 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |

### s1 (Standard) Flavors - Large Root Disk

| Name   | Cores |   RAM |  Disk  | IOPS Limits (read/write) | IO throughput rate (read/write) |
| :----------   | ----: | ----: | ----:  | -----------------------: | ------------------------------: |
| s1.micro.d    |     1 |  2 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.small.d    |     2 |  4 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.medium.d   |     4 |  8 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.large.d    |     8 | 16 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.xlarge.d   |    16 | 32 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| s1.2xlarge.d  |    32 | 64 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |

### m1 (Memory-Optimized) Flavors

| Name | Cores |   RAM |  Disk | IOPS Limits (read/write) | IO throughput rate (read/write) |
| :---------- | ----: | ----: | ----: | -----------------------: | ------------------------------: |
| m1.micro    |     1 |  4 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.small    |     2 |  8 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.medium   |     4 | 16 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.large    |     8 | 32 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.xlarge   |    16 | 64 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.2xlarge  |    32 |128 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |

### m1 (Memory-Optimized) Flavors - Large Root Disk

| Name | Cores |   RAM |  Disk | IOPS Limits (read/write) | IO throughput rate (read/write) |
| :---------- | ----: | ----: | ----: | -----------------------: | ------------------------------: |
| m1.micro.d  |     1 |  4 GB |100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.small.d  |     2 |  8 GB |100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.medium.d |     4 | 16 GB |100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.large.d  |     8 | 32 GB |100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.xlarge.d |    16 | 64 GB |100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| m1.2xlarge.d|    32 |128 GB |100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |

### d1 (Dedicated) Flavors

| Name | Cores |   RAM  |  Disk | IOPS Limits (read/write) | IO throughput rate (read/write) |
| :---------- | -----:| -----: | ----: | -----------------------: | ------------------------------: |
| d1.micro    |    1  |  8 GB  | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.small    |    2  | 16 GB  | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.medium   |    4  | 32 GB  | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.large    |    8  | 64 GB  | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.xlarge   |   16  | 128 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.2xlarge  |   32  | 256 GB | 20 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |

### d1 (Dedicated) Flavors - Large Root Disk

| Name   | Cores |   RAM |  Disk  | IOPS Limits (read/write) | IO throughput rate (read/write) |
| :----------   | ----: | ----: | ----:  | -----------------------: | ------------------------------: |
| d1.micro.d    |     1 |  8 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.small.d    |     2 | 16 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.medium.d   |     4 | 32 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.large.d    |     8 | 64 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.xlarge.d   |    16 |128 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| d1.2xlarge.d  |    32 |256 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |

### g1 (GPU) Flavors - only available in SZ1

| Bezeichnung   |GPU         | Kerne |   RAM |  Disk  | IOPS Limits (read/write) | IO throughput rate (read/write) |
| :----------   |-----------:| ----: | ----: | ----:  | -----------------------: | ------------------------------: |
| g1.xlarge-1g  |1x A100 80GB|    30 |480 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
| g1.2xlarge-2g |2x A100 80GB|    60 |960 GB | 100 GB |              1000 / 1000 |             200 MB/s / 200 MB/s |
