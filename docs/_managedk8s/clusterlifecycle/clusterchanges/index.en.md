---
title: Cluster Changes
lang: "en"
permalink: /managedk8s/clusterlifecycle/clusterchanges/
nav_order: 3800
parent: Cluster Lifecycle
---

We support the following changes to a running CKS cluster:

**Kubernetes Version Upgrade**
- Upgrade your cluster to a newer Kubernetes version.

**Kubeconfig Rotation**
- Rotate the kubeconfig for security or operational needs.

**Control Plane Changes**

- Update the flavor type of the control plane.
- Enable, change or disable the [API server allowlist](/managedk8s/clusterlifecycle/apiallowlist/).

**Machine Deployment Changes**

- Add new machine deployments.
- Delete existing machine deployments.
- Update existing machine deployments, including:
   - Name
   - Replicas
   - Flavor type
   - Taints

**Please Note:** We cannot migrate a volume from one Availability Zone (AZ) to another. If you plan to update the machine deployment's AZ, be aware that this may cause issues with the volumes associated with that machine deployment.

**Custom Root Volume Modifications**
- Add or remove custom root volumes for the control plane or worker nodes, including:
   - Disk size
   - Volume type

**Cluster Autoscaler Feature**
- Enable or disable the cluster-autoscaler for a specific machine deployment.
- Adjust the minimum and maximum number of worker nodes for autoscaling.

**Component Extra Args**
- Pass extra command-line flags to control plane components, the control plane kubelet, or the cluster-autoscaler. See [Component Extra Args](/managedk8s/clusterlifecycle/extraargs/).

## How to request support for your CKS cluster
You can find our support channels [here](/managedk8s/about/support/).

**When requesting changes to a machine deployment, please specify the machine deployment the changes should be applied to. A name in the format `md-name-az-md`**
**or `cluster-name-az-md` is required so that we can identify the correct machine deployment.**

We will handle the requested changes or reach out for more details if needed before applying them to your cluster.
