---
title: Root Disks
lang: "en"
permalink: /managedk8s/clusterlifecycle/rootdisk/
nav_order: 3350
parent: Cluster Lifecycle
---
# Root Disks

You can define disk size and disk type for the controle plane and the worker groups. Please provide this information in the following format:

```
# Controle plane
disk_size_cp: <INT>
volume_type_cp: <vType>

# Worker
# default
disk_size: <INT>
volume_type: <vType>
```

`<INT>` replace this with in positive integer. This is the size of the root disk in Gigabyte. 

`<vType>` is dependent on the used plattform. Please consult the plattform documentation for this information.
- [optimist](/optimist/specs/volume_specification/)

The default value schould be `default` and only be changed if you know that you have different requirements.

# Considerations
* While choosing a disksize make sure you take in to account that the pulled docker images are living on this disks. So if you have a lot of big docekr images adjust your disk size accordingly. 
* If you use a lot of [ephemeral-volumes](https://kubernetes.io/docs/concepts/storage/ephemeral-volumes/) or store / process large amounts of data adjust your disk size accordingly. 
* if you know you have a lot of k8s API `changes` consider a higher iops controle plane root disk.
* If you specify a root disk it will create the corresponding disks in your tenant.

**Hint:** The default volume is if nothing is specified type: default and size: 20GB