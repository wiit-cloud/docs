---
title: Maintenance Window
lang: "en"
permalink: /managedk8s/clusterlifecycle/maintenancewindow/
nav_order: 3320
parent: Cluster Lifecycle
---
# Maintenance Windows

To keep the cluster secure and up to date, several kinds of updates can happen:

* Kubernetes patch version
* Flatcar OS
* Installed apps (e.g. CNI/CPI)
* System

Some of these trigger a node rotation (Flatcar or Kubernetes patch updates), others do not (app updates). Your applications therefore need to be able to handle node rotations at any time.

If no maintenance window is configured, updates can happen at any time. To have more control over when updates happen, we have implemented a maintenance window.

## The Window

To define maintenance windows, we can add multiple time ranges per day to the cluster. **But we need at least a 1-hour window per week!**
This way, we don't accumulate too many pending changes and can be sure that all changes are applied at least once a week.

```
<day of week>: <start time>-<end time>, <start time>-<end time>, ...

Mon: 06:00-10:00, 17:00-20:00
```

Some important cases:
* A started maintenance will always finish, even if it takes longer than the configured window.
* When you make a change to your cluster (e.g. more replicas, new minor version, etc.), all pending changes will be applied at the same time.

**In rare cases, when we have breaking changes or highly important CVEs, we can schedule global maintenance on short notice.**
