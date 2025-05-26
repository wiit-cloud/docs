---
title: Cluster Deletion
lang: "en"
permalink: /managedk8s/clusterlifecycle/clusterdeletion/
nav_order: 3900
parent: Cluster Lifecycle
---

As a customer, you are responsible for cleaning up all applications and resources, and stopping any automation running inside the cluster before requesting its deletion.

Once all applications, resources, and automation processes have been deleted or stopped, you can request cluster deletion via your preferred support channel (Microsoft Teams, Slack, Helpdesk, Email) by providing the [WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name) to us. Then, we will proceed with deleting the cluster.

If any applications or resources are still running inside the cluster, we will perform a forced deletion. You will then be responsible for manually cleaning up any leftover resources in Openstack.
