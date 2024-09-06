---
title: Root Disks
lang: "en"
permalink: /managedk8s/clusterlifecycle/rootdisk/
nav_order: 3350
parent: Cluster Lifecycle
---
# Root Disks

There are 2 places in the `values.yaml` where you can define your VM Storage. For the controle plane use:

```yaml
use_custom_disk_cp: false
disk_size_cp: 0
volume_type_cp: ''
```

And for the workers (can be configured per worker group) use:
```yaml
workers:
- name: example
  use_custom_disk: false
  disk_size: 20
  volume_type: <vType>
```

`<vType>` is dependent on the used plattform. Please consult the plattform documentation for this information.
- [optimist](/optimist/specs/volume_specification/)

The default value schould be `default` and only be changed if you know that you have different requirements. 

# Considerations
* While choosing a disksize make sure you take in to account that the pulled docker images are living on this disks. So if you have a lot of big docekr images adjust your disk size accordingly. 
* If you use a lot of [ephemeral-volumes](https://kubernetes.io/docs/concepts/storage/ephemeral-volumes/) or store / process large amounts of data adjust your disk size accordingly. 
* if you know you have a lot of k8s API `changes` consider a higher iops controle plane root disk.