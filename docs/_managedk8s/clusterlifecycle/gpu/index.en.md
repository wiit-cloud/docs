---
title: GPU Worker
lang: "en"
permalink: /managedk8s/clusterlifecycle/gpu/
nav_order: 3520
parent: Cluster Lifecycle
---
# GPU Worker Nodes

We support the [Openstack Nvidia/GPU Flavors](https://docs.wiit-cloud.io/openstack/specs/flavor_specification/#g1-gpu-flavors---only-available-in-sz1) in our Kubernetes clusters.

You just need to select the right Flavor on your worker group, and it will have GPU support.
The GPU nodes are handled the same way as the normal worker groups, so most things that apply to them applies the GPU.

## Some extras:
### Node Labels
We use the GPU Discovery service and set a Bunch of Labels on the node, so all GPU information is on that place.

### Maintenance
The GPU machine deployments uses the same Maintenance Window. 
In addition, we have a second [Maintenance from Openstack](https://docs.wiit-cloud.io/openstack/intro/#maintenance). 
To be consistent we expose the Openstack Metadata as a node label in k8s.

So in case of a Openstack Maintenance we set the node label `openstack.cks.wiit-cloud.io/maintenance_planned_for` with the Reboot date as value.

For all other features look into the [Machine Deployment](https://docs.wiit-cloud.io/managedk8s/clusterlifecycle/machinedeployments/).
