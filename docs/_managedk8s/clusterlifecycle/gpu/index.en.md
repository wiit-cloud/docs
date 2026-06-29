---
title: GPU Worker
lang: "en"
permalink: /managedk8s/clusterlifecycle/gpu/
nav_order: 3520
parent: Cluster Lifecycle
---
# GPU Worker Nodes

We support the [Openstack Nvidia/GPU Flavors](https://docs.wiit-cloud.io/openstack/specs/flavor_specification/#g1-gpu-flavors---only-available-in-sz1) in our Kubernetes clusters.

Simply select the appropriate GPU-enabled flavor for your worker group to enable GPU support.

GPU worker nodes are managed in the same way as regular worker nodes, so most configuration and operational considerations apply equally to both.

## Some extras:
### Node Labels
We use the GPU Discovery service to apply a set of labels to each node, keeping all GPU-related information in one place.

### Maintenance
GPU machine deployments use the same maintenance window as regular machine deployments. 
In addition, OpenStack has its own [maintenance mechanism](https://docs.wiit-cloud.io/openstack/intro/#maintenance).

For consistency, we expose the relevant OpenStack metadata as Kubernetes node labels.

When an OpenStack maintenance that requires a hypervisor reboot is scheduled, we set the `openstack.cks.wiit-cloud.io/maintenance_planned_for` node label to the planned reboot date.

For all other features look into the [Machine Deployment](https://docs.wiit-cloud.io/managedk8s/clusterlifecycle/machinedeployments/).
