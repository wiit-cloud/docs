---
title: Maintenance Window
lang: "en"
permalink: /managedk8s/clusterlifecycle/maintenacewindow/
nav_order: 3320
parent: Cluster Lifecycle
---
# Maintenance Windows

To keep the cluster secure and up to date, there are a bunch of possible updates that can happen. F.e. K8s Version updates, Flatcar OS, controller updates or system updates.

Some of these trigger a node rotation (Flatcar or K8S updates), some not (controller updates). So the application needs to handle node rotations at any time. 

If no maintenance window is configured, the updates will happen when they happen. To have a more control when updates should happen, we have implemented a maintenance window.


## The Window

To define maintenance windows, we can add multiple time ranges per day to the cluster. **But we need a least a 1-hour windows per week!**
So we don't have to many pending changes and can be sure that all changes are applied at least once a week.

```
<day of week>: <start time>-<end time> , <start time>-<end time>, .....

Mon: 06:00-10:00, 17:00-20:00
```

Some imported cases:
* A started maintenance will always finish, even if it takes longer than the configured window.
* When you do a change to your cluster (f.e. more replica), all pending changes will be applied at the same time.
