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

The Cluster Nodes Autoscaler is not enabled by default. To enable it, please contact us using the instructions provided [here](/about/support/).

When reaching out, include the following information:
- The cluster ID and Name
- The Specific Machine Deployment(s) Name
- Minimum Number of Worker Nodes in the specific Machine Deployment: Define the minimum number of nodes that should always be maintained in the specific Machine Deployment.
- Maximum Number of Worker Nodes: Define the maximum number of nodes in the specific Machine Deployment the cluster can scale up to.

It is possible to enable or disable the cluster-autoscaler feature anytime in one or multiple machineDeployments.

**Note:** Ensure pods have appropriate resource requests and limits to make the autoscaling effective.
