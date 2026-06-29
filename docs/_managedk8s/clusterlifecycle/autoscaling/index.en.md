---
title: Cluster Autoscaler
lang: "en"
permalink: /managedk8s/clusterlifecycle/autoscaling/
nav_order: 3510
parent: Cluster Lifecycle
---
# Cluster Node Autoscaler
The Cluster Node Autoscaler automatically adjusts the number of nodes in a Kubernetes cluster to match the current workload. It ensures efficient resource utilization by scaling the cluster up when there is insufficient capacity to run all scheduled pods and scaling it down when there are unused nodes.

# How the Cluster Autoscaler Works:
**Scaling Up:**
When the autoscaler detects that some pods cannot be scheduled due to insufficient resources (CPU, memory), it will automatically add new nodes to the cluster to provide the necessary capacity.

**Scaling Down:**
If the autoscaler identifies nodes that are underutilized or completely not used for a configurable period, it will remove those nodes to optimize resource usage and reduce costs. Before scaling down, it ensures that there are no critical pods running on those nodes and that workloads can be safely moved to other nodes.

The Cluster Nodes Autoscaler is not enabled by default. To enable it, please contact us using the instructions provided [here](/managedk8s/about/support/).

When reaching out, include the following information:

- [WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name)
- The Specific Machine Deployment(s) Name
- Minimum Number of Worker Nodes in the specific Machine Deployment: Define the minimum number of nodes that should always be maintained in the specific Machine Deployment.
- Maximum Number of Worker Nodes: Define the maximum number of nodes in the specific Machine Deployment the cluster can scale up to.

It is possible to enable or disable the cluster-autoscaler feature anytime in one or multiple machineDeployments.

**Note:** Ensure pods have appropriate resource requests and limits to make the autoscaling effective.

# Two levels of configuration

The autoscaler has two scopes. You can use either or both.

## Per worker group

Enabling the autoscaler and setting the minimum and maximum node count are per worker group.
You can also tune the scale-down and provisioning behaviour for a single worker group. Each
option overrides the cluster-wide default for that group only:

| Option | Controls |
|---|---|
| `scaledownutilizationthreshold` | Usage below which a node is considered for removal (e.g. `0.5`). |
| `scaledowngpuutilizationthreshold` | Same threshold for GPU nodes. |
| `scaledownunneededtime` | How long a node must be unneeded before removal (e.g. `10m0s`). |
| `scaledownunreadytime` | How long an unready node waits before removal. |
| `maxnodeprovisiontime` | How long to wait for a new node to register. |
| `maxnodestartuptime` | How long to wait for a node to become ready. |

## Cluster-wide

The autoscaler runs as a single process for the whole cluster. Process-level flags apply to
every worker group at once. You set them through extra args (see
[Component Extra Args](/managedk8s/clusterlifecycle/extraargs/)).

To request either scope, open a [support request](/managedk8s/about/support/) with your
[WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name), the worker group name for
per-group changes, and the options or flags you want.

## Scale from 0

We support scale from zero. You can set the minimum nodes to 0, and when there are no pods, the autoscaler will scale down to 0 worker.
