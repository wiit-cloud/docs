---
title: Flavor Specifications
lang: "en"
permalink: /optimist/specs/flavor_specification/
parent: Specifications
nav_order: 9100
last_modified_date: 2022-03-29
---

# Flavor Specifications

In the OpenStack context the term "flavor" refers to a hardware profile that can be used for a virtual machine. In Optimist we have set up
various standard hardware profiles (flavors). These have different limits, which are listed below for all available flavors.

## Migrating between Flavor Types

To change the flavors of existing instances, the OpenStack ["Resize Instance"](/optimist/faq/#how-can-i-change-the-flavor-of-a-virtual-machine-instance-resize) Option can be used either via the Dashboard or the CLI. This will result in a reboot of the Instance but the content of the instance will be preserved. Please note that changing Flavors from Large Root Disk Types to a Flavor with a smaller Root Disk is not possible.

{: .warning }
This does not apply to l1 (localstorage) flavors
For more information please see [Storage → Localstorage](/optimist/storage/localstorage/#openstack-features)

## Flavor Types

### Standard Flavors

| Name       | Cores |   RAM |  Disk | IOPS Limits (read/write) | IO throughput (read/write) | Network Bandwidth |
| :--------- | ----: | ----: | ----: | -----------------------: | -------------------------: | ----------------: |
| s1.micro   |     1 |  2 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          1 Gbit/s |
| s1.small   |     2 |  4 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          2 Gbit/s |
| s1.medium  |     4 |  8 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          3 Gbit/s |
| s1.large   |     8 | 16 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |
| s1.xlarge  |    16 | 32 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |
| s1.2xlarge |    30 | 64 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |

### Standard CPU Large Disk Flavors

| Name        | Cores |   RAM  |  Disk  | IOPS Limits (read/write) | IO throughput (read/write)      | Network Bandwidth |
| :----------   | ----: | ----:  | ----:  | -----------------------: | ------------------------------: | ----------------: |
| s1.micro.d    |     1 |  2 GB | 100 GB  |              2500 / 2500 |             250 MB/s / 250 MB/s  |          1 Gbit/s |
| s1.small.d    |     2 |  4 GB | 100 GB  |              2500 / 2500 |             250 MB/s / 250 MB/s  |          2 Gbit/s |
| s1.medium.d   |     4 |  8 GB | 100 GB  |              2500 / 2500 |             250 MB/s / 250 MB/s  |          3 Gbit/s |
| s1.large.d    |     8 | 16 GB | 100 GB  |              2500 / 2500 |             250 MB/s / 250 MB/s  |          4 Gbit/s |
| s1.xlarge.d   |    16 | 32 GB | 100 GB  |              2500 / 2500 |             250 MB/s / 250 MB/s  |          4 Gbit/s |
| s1.2xlarge.d  |    30 | 64 GB | 100 GB  |              2500 / 2500 |             250 MB/s / 250 MB/s  |          4 Gbit/s |

### Dedicated CPU Flavors

| Name       | Cores |   RAM  |  Disk | IOPS Limits (read/write) | IO throughput (read/write) | Network Bandwidth |
| :--------- | ----: | -----: | ----: | -----------------------: | -------------------------: | ----------------: |
| d1.micro   |     1 |  8 GB  | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          1 Gbit/s |
| d1.small   |     2 | 16 GB  | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          2 Gbit/s |
| d1.medium  |     4 | 32 GB  | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          3 Gbit/s |
| d1.large   |     8 | 64 GB  | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |
| d1.xlarge  |    16 | 128 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |
| d1.2xlarge |    30 | 256 GB | 20 GB |              1000 / 1000 |        200 MB/s / 200 MB/s |          4 Gbit/s |

### Dedicated CPU Large Disk Flavors

| Name        | Cores |   RAM  |  Disk  | IOPS Limits (read/write) | IO throughput (read/write)      | Network Bandwidth |
| :----------   | ----: | ----:  | ----:  | -----------------------: | ------------------------------: | ----------------: |
| d1.micro.d    |     1 |  8 GB  | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          1 Gbit/s |
| d1.small.d    |     2 | 16 GB  | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          2 Gbit/s |
| d1.medium.d   |     4 | 32 GB  | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          3 Gbit/s |
| d1.large.d    |     8 | 64 GB  | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          4 Gbit/s |
| d1.xlarge.d   |    16 |128 GB  | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          4 Gbit/s |
| d1.2xlarge.d  |    30 |256 GB  | 100 GB |              2500 / 2500 |             250 MB/s / 250 MB/s |          4 Gbit/s |

### Localstorage Flavors

| Name       | Cores |   RAM  |  Disk | IOPS Limits (read/write) | IO throughput (read/write) | Network Bandwidth |
| :---------- | -----: | -----: | -------: | -----------------------: | ------------------------------: | ----------------: |
| l1.micro    |    1   |   8 GB |   300 GB |            25000 / 10000 |             125 MB/s / 60 MB/s  |          1 Gbit/s |
| l1.small    |    2   |  16 GB |   600 GB |            50000 / 25000 |             250 MB/s / 125 MB/s |          2 Gbit/s |
| l1.medium   |    4   |  32 GB |  1200 GB |           100000 / 50000 |             500 MB/s / 250 MB/s |          3 Gbit/s |
| l1.large    |    8   |  64 GB |  2500 GB |          100000 / 100000 |            1000 MB/s / 500 MB/s |          4 Gbit/s |
| l1.xlarge   |   16   | 128 GB |  5000 GB |          100000 / 100000 |           2000 MB/s / 1125 MB/s |          4 Gbit/s |
| l1.2xlarge  |   30   | 256 GB | 10000 GB |          100000 / 100000 |           2000 MB/s / 2000 MB/s |          4 Gbit/s |

L1 / Localstorage Flavors can be requested from <support@gec.io>
