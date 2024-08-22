---
title: Cluster Autoscaler
lang: "en"
permalink: /managedk8s/clusterlifecycle/autoscaling/
nav_order: 3510
parent: Cluster Lifecycle
---
# Cluster Node Autoscaler

TODO: create extra topic for autoscaler and add here

* Provide the machineDeployment name, the format of the machineDeployment name will be `md-name-az-md` for example `md-autoscaler-test-ix1-md`.
  If the md-name is not provided, the format of the machineDeployment name will be `cluster-name-az-md` for example `cluster-test-es1-md`
* Enable - autoscaler is not enabled by default
* If enabled, provide the desired `min` and `max` number of worker nodes


