---
title: Node rotation
lang: "en"
permalink: /managedk8s/clusterlifecycle/noderotation/
nav_order: 3350
parent: Cluster Lifecycle
---

# Node rotation
There are several possible reasons why and when a node rotation can occur. Here some examples:

* updates on the OS image
* configuration changes
* Hardware maintance
* Hardware failure

Some of there are more or less planed and some of them happens unplaned.

## What happens in a node rotation
The Flow of of a rotation is quite simple. There are a number of steps that will be performed automatically.

1. All nodes get a NoSchedule taint
2. For each class a new VM is spawnd and integrated in the cluster
3. If the node is successfully integrated one node of the same Type get drained 
4. After the node is drained the node gets deleted

This spawn, drain and delete cycle goes on until all nodes are rotated. 

These rotation effects also the controle-plane nodes. This has an very short impact on the availability of the API. The takeover of the leader needs some seconds (k8s api and etcd). In this time the API is not responsive. But all processes and Services should run without problems.

# Note

* So please note there is no guarantee that the API or a node has a 100% uptime / availability. 
* Your software should Account for this. There are several kubernetes mechanics to help with that.
  * [liveness, readiness and startup probes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)
  * [drain nodes](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)
  * [disruption budged](https://kubernetes.io/docs/tasks/run-application/configure-pdb/)

