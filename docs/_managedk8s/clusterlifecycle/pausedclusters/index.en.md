---
title: Paused Clusters
lang: "en"
permalink: /managedk8s/clusterlifecycle/pausedclusters/
nav_order: 3325
parent: Cluster Lifecycle
---
# Paused Clusters

You can pause a cluster with the field `spec.paused` of the WorkloadCluster:

```yaml
spec:
  paused: true
```

While a cluster is paused, we do not make changes to the cluster. Use this only for a short time, for example to keep the cluster stable during an important event.

**A paused cluster gets no updates, also no security patches. While the cluster is paused, you are responsible for its security and its operation.**

## What stops while the cluster is paused

* Changes to the cluster spec. You can still change the spec, but we apply the changes only after you resume the cluster. This includes all changes on [Cluster Changes](/managedk8s/clusterlifecycle/clusterchanges/), for example machine deployments, flavors and the Kubernetes version.
* Kubernetes patch updates.
* Flatcar OS updates.
* Updates of the installed apps, for example the CNI or the CPI.
* The [maintenance window](/managedk8s/clusterlifecycle/maintenancewindow/) has no effect. Also global maintenance on short notice, for example for important CVEs, does not reach a paused cluster.

## What continues while the cluster is paused

* The cluster and your workloads continue to run.
* We continue to update the status of the WorkloadCluster.

## Resume the cluster

To resume the cluster, set `spec.paused` to `false` or remove the field.

This is a change to the cluster. Thus we apply **all** pending changes immediately, also outside of your maintenance window. Pending changes are the changes to the spec that you made while the cluster was paused, and all updates that we released in this time. If the cluster was paused for a long time, this can be a large update with node rotations.
