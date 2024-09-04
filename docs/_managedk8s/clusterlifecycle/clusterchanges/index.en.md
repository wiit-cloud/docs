---
title: Cluster Changes
lang: "en"
permalink: /managedk8s/clusterlifecycle/clusterchanges/
nav_order: 3800
parent: Cluster Lifecycle
---

We can support the following updates in a running GKSv3 cluster:

**Kubernetes Version Upgrade**
- Upgrade your cluster to a newer Kubernetes version.

**Kubeconfig Rotation**
- Rotate the kubeconfig for security or operational needs.

**Control Plane Changes**

- Update the flavor type of the control plane.

**Machine Deployment Changes**

- Add new machine deployments.
- Delete existing machine deployments.
- Update existing machine deployments, including:
   - Name
   - Replicas
   - Flavor type

**Please Note:** We cannot migrate a volume from one Availability Zone (AZ) to another. If you plan to update the machine deployment's AZ, be aware that this may cause issues with the volumes associated with that machine deployment.

**Custom Root Volume Modifications**
- Add or remove custom root volumes for the control plane or worker nodes, including:
   - Disk size
   - Volume type

**Cluster Autoscaler Feature**
- Enable or disable the cluster-autoscaler for a specific machine deployment.
- Adjust the minimum and maximum size of worker nodes to enable autoscaling.

## How to request support for your GKSv3 cluster
You can find the list of the Support [here](/gks/about/support/)

**In case of requesting changes in the machineDeployment, please specify the machineDeployment where the changes should be applied. A format like `md-name-az-md`**
**or `cluster-name-az-md`is required so that we refer to the correct machineDeployment.**

We will handle the requested changes or reach out for more details if needed before applying them to your cluster.
