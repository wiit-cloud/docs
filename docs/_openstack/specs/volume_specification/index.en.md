---
title: Volume Type Specifications
lang: "en"
permalink: /openstack/specs/volume_specification/
parent: Specifications
nav_order: 9101
last_modified_date: 2025-07-21
---

# Volume Specifications

In OpenStack, “volumes” are persistent storage that you can attach to your running OpenStack Compute instances to. In openstack we have set up three classes of service for volumes. These have different limits, which are detailed below.

## Volume Types

We have two main volume types:

* high-iops
* standard

## Volume Type List

An overview of the two volume types below:

| Name          | Read Bytes Sec | Read IOPS Sec  | Write Bytes Sec | Write IOPS Sec |
| :------------ | -------------: | -------------: | --------------: | -------------: |
| high-iops     | 524288000      | 10000          | 524288000       | 10000          |
| standard      | 209715200      | 2500           | 209715200       | 2500           |

## Choosing a Volume Type

You can select one of the two volume types upon creation of a volume with the following command (Unless otherwise specified, the type "default" is always used):
`$ openstack volume create <volume-name> --size 10 --type high-iops`

## Changing the Volume Type

You can adjust the selected type of a volume. To do so, you can use the "Change Volume Type" function in the dashboard. Alternatively, you can change the type via the CLI with the following command:
`$ openstack volume set <volume-name> --type high-iops`
However, changing the volume type is only possible if the volume is not currently in use by an instance.

## Volume Group Specifications

In OpenStack, "Volume Groups" provide a way to logically group multiple volumes. This simplifies management (e.g., creating snapshots for all volumes in a group) and helps organize complex application environments.

## Volume Group Type List

We currently provide the following volume group type:

| Name | Description | Consistency Support | Public |
| :--- | :--- | :--- | :--- |
| **standard_group** | Standard grouping for related volumes. | No | Yes |

> **NOTE:** The `standard_group` type serves as a logical container. However, it does not support cross-volume consistency (Consistency Groups). Snapshots are taken sequentially.

## Using Volume Groups

### 1. Creating a Volume Group

To create a new group, you must specify the `standard_group` type:

```bash
openstack volume group create --type standard_group <group-name>
```

### 2. Adding Volumes to a Group

A volume can be assigned to a group during its creation:

```bash
openstack volume create --size 10 --group <group-name> <volume-name>
```

### 3. Creating Group Snapshots

You can initiate a common snapshot point for all volumes within the group:

```bash
openstack volume group snapshot create --group <group-name> <snapshot-name>
```
