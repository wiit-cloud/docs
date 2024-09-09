---
title: Machine Deployments
lang: "en"
permalink: /managedk8s/clusterlifecycle/machinedeployments/
nav_order: 3500
parent: Cluster Lifecycle
---
# Machine Deployments

## Machine Deployment Creation
- We are responsible for creating machine deployments based on the your requirements.
- Each machine deployment will be configured to meet specific needs such as compute capacity, storage, and availibily zone.
- Please provide the name of the machineDeployment(s). If the machine deployment name is not provided, the name will default to the format clusterName-az-md (e.g., cluster-test-ix2-md).
- We can enable autoscaler for you, please see (here) [/managedk8s/clusterlifecycle/autoscaling/]
* The default machine deployment configuration is the following:
```yaml
workers:
- name: clusterName-az-md
  replicas: 3
  autoscaler:
    min: 0
    max: 0
  availability_zones:
  - <random-az>
  roles:
  - "worker"
  restrictions: []
  machine_type: s1.large
  use_custom_disk: false
```
If you need any other changes in the configuration, please mention them when requesting a new machine deployment. However, first, please review the following configuration details and limitations:
 * Flavor/Machine Type - the default type is s1.large (`8 cores`, `16GB RAM` and `20GB` disk size), you can select each flavor that you can see in your OpenStack project, but make sure the flavor:
    * has at least 2 cores and 2 GB RAM
    * is not a windows image
* Number of replicas  the default is 3 nodes. 
* Availability zone (AZ) - if preferred, specify the AZ from `ix1`, `ix2` or `es1` available zones. The default value will be a random AZ.
  * If there are multiple AZs configured, there will be a machineDeployment created for each AZ with the specified replica count. For example if you want to use 3 replicas with AZs ix1 and ix2, there will be 2 machineDeployments each with 3 replicas.
* Custom root disk size and/or volume type - if not enabled foe more information please see here[/managedk8s/clusterlifecycle/rootdisks]
* roles are set as labels on the nodes. where the labels have the following format: `node-role.kubernetes.io/<ROLENAME>: ""`
* restrictions are set as labels on the nodes. where the labels have the following format: `node-role.kubernetes.io/<RESTRICTIONNAME>: ""`

Please be aware that we only accept this format for role and restriction: **node-role.kubernetes.io/NAME: ""**

## Machine Deployment Updates

- We can update the machine deployment for you, you can:
  * update the flavor
  * update replicas
  * update the application credentials
  * enable/disable the autoscaler
  * enable/disable oidc
- We can create a new machine deployment(s) for you.
- Please be aware that: 
  * We can not update the availibity zone , as the volumes will be stack in the old availibility zone.
  * We can not create a machine deployment in different availibility zone, if you need multiple availibility zone you need to create multiple machinedeployment in each availibility zone. 
  * We will regularly update the os to ensure they are running the latest software versions, patches, and security updates.

## Node Rotation
We are rotating the nodes in your Kubernetes cluster to ensure optimal performance, security, and reliability. 
We are rotating the nodes of all the machinedeployments on the same time. The process is the following: 
* Draining the nodes(s): We start by safely draining each node(s) to gracefully evict all running pods.
* Creating a new one(s): A new node(s) is created to replace the drained one
* Deleting the old node(s): Once the new node is fully operational and all workloads are running smoothly, the old node is deleted. 

This process is applied to all nodes in the cluster's machine deployments, and it's performed periodically or whenever needed to maintain the desired state of the cluster.

Node rotation is necessary for several reasons, including:

**Security Updates:** Applying critical security patches and updates to the operating system.

**Performance Improvements:** Upgrading to newer versions of the Kubernetes.

**Resource Optimization:** Adjusting node configurations.

## What You Need to Take Care Of:
**Workload Stability:** Ensure that your applications are resilient to potential disruptions. Kubernetes will attempt to reschedule your workloads automatically, but some applications may experience brief downtime or require manual intervention.

**Pod Disruption Budgets:** Review and, if necessary, update your Pod Disruption Budgets (PDBs) to control the number of pods that can be safely evicted during the rotation without impacting application availability.


By taking these steps, you can help ensure a smooth node rotation with minimal impact on your services. If you have any questions or need further assistance, please let us know.