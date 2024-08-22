---
title: Cluster Changes
lang: "en"
permalink: /managedk8s/clusterlifecycle/clustercanges/
nav_order: 3800
parent: Cluster Lifecycle
---
This section of the documentation describes what can we support to be updated in a running GKSv3 cluster:

* K8S version upgrade
* Rotate the kubeconfig
* ControlPlane changes
  * update number of replicas
  * update flavor type
* MachineDeployment changes
  * add new machineDeployment in other availability zones
  * delete existing machineDeployment
  * update existing machineDeployment
    * replicas
    * availability zone
    * flavor type
* Add/Remove custom root volume (in controlPlane and/or worker nodes)
  * disk size
  * volume type
* Autoscaler feature in the cluster
  * enable/disable cluster-autoscaler in the specified machineDeployment
  * min and max size of worker nodes if required to enable this feature
* Maintenance window
  * new cron syntax

## How to request support for your GKSv3 cluster

There are two ways to ask support for your cluster, one is sufficent to be followed:

* Write a message in our Slack channel [#product_gks](https://app.slack.com/client/T3Q3RTXQF/C8FJLPPEU) to get support:
  * provide your clusterID
  * specify your requirement
* Create an [HelpDesk ticket](https://jira.gec.io/servicedesk/customer/portal/44/create/705) in the internal helpdesk
  * include one of the above points in the summary to describe what is the request for the change in the cluster
  * provide your clusterID
  * describe your requirement

**In case of requesting changes in the machineDeployment, please specify the machineDeployment where the changes should be applied. A format like `md-name-az-md`**
**or `cluster-name-az-md`is required so that we refer to the correct machineDeployment.**

We will take care of the change you will need in your cluster or ask for more details before we apply the changes.


## Conclusion

All the above steps should enable you to get support in your running GKSv3 clusters.

