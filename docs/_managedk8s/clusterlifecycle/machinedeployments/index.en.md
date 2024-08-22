---
title: Machine Deployments
lang: "en"
permalink: /managedk8s/clusterlifecycle/machinedeployments/
nav_order: 3200
parent: Cluster Lifecycle
---
# Machine Deployments

TODO: add workernode docu with a lot more details -> multiaz howto, roles/restrictuions and link disk again

* Provide the machineDeployment name (in case of multiple ones), the format of the machineDeployment name will be `md-name-az-md` for example `md-autoscaler-test-ix1-md`.
  If the md-name is not provided, the format of the machineDeployment name will be `cluster-name-az-md` for example `cluster-test-ix2-md`.
* In the generated values.yaml will be an entry for each machineDeployment which can be configured individually. The defaults are:
```yaml
workers:
- name: md-name
  replicas: 3
  autoscaler:
    min: 0
    max: 0
  availability_zones:
  - <random-az>
  roles:
  - "worker"
  restrictions: []
  machine_type: s1.large
  use_custom_disk: false
  disk_size: 20
  volume_type: default  
```
* Flavor/Machine Type - default type is s1.large (`8 cores`, `16GB RAM` and `20GB` disk size)
  You can select each flavor that you can see in your OpenStack project, but make sure the flavor:
  * has at least 2 cores and 2 GB RAM
  * is not a windows image
* Number of replicas - integer value, the default is 3 nodes
* Availability zone (AZ) - if preferred, specify the AZ from `ix1`, `ix2` or `es1` available zones. The default value will be a random AZ.
  If there are multiple AZs configured, there will be a machineDeployment created for each AZ with the specified replica count. For example if you want to use 3 replicas with AZs ix1 and ix2, there will be 2 machineDeployments each with 3 replicas.
* Custom root disk size and/or volume type - if not enabled, defaults are the flavor limits (volume type options: `low-iops`, `high-iops`, `default`)
* roles are set as labels on the nodes. where the labels have the following format: `node-role.kubernetes.io/<ROLENAME>: ""`
* restrictions are set as labels on the nodes. where the labels have the following format: `node-role.kubernetes.io/<RESTRICTIONNAME>: ""`
