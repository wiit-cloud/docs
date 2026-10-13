---
title: Root Disks
lang: "en"
permalink: /managedk8s/clusterlifecycle/rootdisk/
nav_order: 3355
parent: Cluster Lifecycle
---
# Root Disks

You can define disk size and disk type for the control plane and the worker groups. Please provide this information in the following format:

```
# Control plane
disk_size_cp: <INT>
volume_type_cp: <vType>

# Worker
# default
disk_size: <INT>
volume_type: <vType>
```

Replace `<INT>` with a positive integer. This is the size of the root disk in Gigabyte.

`<vType>` is dependent on the used platform. Please consult the platform documentation for this information.
- [OpenStack](/openstack/specs/volume_specification/)

The default value should be `default` and only be changed if you know that you have different requirements.

# Considerations
* While choosing a disksize, make sure you take into account that the pulled docker images are living on this disk. So if you have a lot of big docker images, adjust your disk size accordingly. 
* If you use a lot of [ephemeral-volumes](https://kubernetes.io/docs/concepts/storage/ephemeral-volumes/) or store / process, large amounts of data adjust your disk size accordingly. 
* if you know you have a lot of k8s API `changes` consider a higher iops control plane root disk.
* If you specify a root disk, it will create the corresponding disks in your tenant.

**Hint:** The default volume is if nothing is specified type: default and size: 20GB
