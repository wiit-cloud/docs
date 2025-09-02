---
title: Maintenance Window
lang: "en"
permalink: /managedk8s/clusterlifecycle/maintenacewindow/
nav_order: 3320
parent: Cluster Lifecycle
---
# Maintenance Windows

To keep the cluster secure and up to date, there are a bunch of possible updates that can happen. 

* K8s patch level version 
* Flatcar OS, 
* Installed Apps (like cni/cpi)
* System

Some of these trigger a node rotation (Flatcar or K8S patch level updates), some not (App updates). So the application needs to be able to handle node rotations at any time. 

If no maintenance window is configured, updates can happen anytime To have a more control when updates should happen, we have implemented a maintenance window.


## The Window

To define maintenance windows, we can add multiple time ranges per day to the cluster. **But we need a least a 1-hour windows per week!**
So we don't have to many pending changes and can be sure that all changes are applied at least once a week.

```
<day of week>: <start time>-<end time> , <start time>-<end time>, .....

Mon: 06:00-10:00, 17:00-20:00
```

Some important cases:
* A started maintenance will always finish, even if it takes longer than the configured window.
* When you do a change to your cluster (e.g. more replica, new minor version, etc.), all pending changes will be applied at the same time.


**In rare cases, when we have breaking changes or high important CVEs, we can schedule global maintenance on short notice.**
