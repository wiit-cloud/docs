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
- We can enable autoscaler for you, please see here [/managedk8s/clusterlifecycle/autoscaling/]
* The default machine deployment configuration is the following:
```yaml
workers:
- name: md-name
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
  disk_size: 20
  volume_type: default  
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

## Machine Deployment Updates

- We will regularly update machine deployments to ensure they are running the latest software versions, patches, and security updates.
- Updates will be scheduled during agreed maintenance windows to minimize impact on operations.
- Critical updates will be prioritized and carried out outside the scheduled maintenance windows to ensure the highest level of security and stability.

## Node Rotation
Node rotation is the process of replacing nodes within a machine deployment to maintain the infrastructure's health and performance. The process involves systematically draining workloads from node scheduled for rotation, creating new one, terminating old one, and replacing it with new one.

We are rotating the nodes in your Kubernetes cluster to ensure optimal performance, security, and reliability. Node rotation is necessary for several reasons, including:

**Security Updates:** Applying critical security patches and updates to the operating system or underlying infrastructure to protect against vulnerabilities.

**Performance Improvements:** Upgrading to newer versions of the Kubernetes node that enhance performance and efficiency.

**Resource Optimization:** Adjusting node configurations to better utilize resources, balance workloads, and improve overall cluster health.

## What You Need to Take Care Of:
**Workload Stability:** Ensure that your applications are resilient to potential disruptions. Kubernetes will attempt to reschedule your workloads automatically, but some applications may experience brief downtime or require manual intervention.

**Pod Disruption Budgets:** Review and, if necessary, update your Pod Disruption Budgets (PDBs) to control the number of pods that can be safely evicted during the rotation without impacting application availability.

**Monitoring and Alerts:** Keep monitoring and alerting systems active to detect and respond to any unexpected issues that might arise during the rotation process.

By taking these steps, you can help ensure a smooth node rotation with minimal impact on your services. If you have any questions or need further assistance, please let us know.