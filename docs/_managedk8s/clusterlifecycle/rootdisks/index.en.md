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

`<vType>` depends on the platform used. Please consult the platform documentation for this information.
- [OpenStack](/openstack/specs/volume_specification/)

The default value is `default` and should only be changed if you know that you have different requirements.

## Considerations
* When choosing a disk size, keep in mind that pulled container images are stored on this disk. If you have a lot of large images, adjust your disk size accordingly.
* If you use a lot of [ephemeral volumes](https://kubernetes.io/docs/concepts/storage/ephemeral-volumes/) or store or process large amounts of data, adjust your disk size accordingly.
* If you expect a high rate of Kubernetes API changes, consider a higher-IOPS root disk for the control plane.
* If you specify a root disk, the corresponding volumes are created in your tenant.

**Hint:** If nothing is specified, the default volume type is `default` and the size is 20GB.
