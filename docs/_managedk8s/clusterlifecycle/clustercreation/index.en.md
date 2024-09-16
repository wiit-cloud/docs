---
title: Cluster Creation
lang: "en"
permalink: /managedk8s/clusterlifecycle/clustercreation/
nav_order: 3100
parent: Cluster Lifecycle
---
# Cluster creation

To successfully create a cluster, please provide the required information outlined below. Ensure all details are filled correctly before proceeding with the cluster creation.
Copy the block below and complete it with the necessary information for your new cluster:
```
# required infromation
cluster_name: 
customer_id: 

# other k8s version
(by default latest k8s version provided by us)
kubernetes_version: 

# controlplane flavor 
# by default s1.medium
flavor: 

## Machine deployments one block per MD (default: 1 md, 3 replicas, s1.large, random AZ)
# machine deployment name
# by default clusterName-az-md
md_name:  
# replicas by default 3
replicas:
# by default s1.large
flavor: 
# by default random-az
availability_zone: 

## add the autoscaler (default: disabled)
min_size:
max_size:
```
Detailed information about options can be found below.

### What You Will Receive
After the cluster is created, you will receive an admin kubeconfig through a secure method, along with a unique Cluster ID.

**For further communication we need the cluster id and cluster name to identify your cluster.**
# Detailed Information

## Prerequisites
### OpenStack Credentials
You must have an existing or newly created OpenStack tenant. In this tenant, create and provide the following:
- [Application credentials](/managedk8s/clusterlifecycle/appcredentials/)linked to the project where you want the new cluster to be created.


### Required Information for the New Cluster
* Cluster Name: A maximum of 22 characters.
* Customer ID: Defaults to the company ID number if not specified.
* Application Credentials: As mentioned in the prerequisites.

### Optional Requirements and Configurable Features (with Default Values)
#### Kubernetes Version
If not specified, the cluster will be deployed with the latest supported Kubernetes minor version.
For more details on supported versions, deprecations, EOL, or other version concerns, [click here](/managedk8s/about/kubernetesverions/)

#### OpenStack Network
Provide an existing network ID, or we will create a new network for you.


#### ControlPlane
Every cluster will have 3 Control Plane nodes distributed across all Availability Zones (AZs) to ensure maximum reliability.

* **Flavor**: The default flavor is s1.medium (4 cores, 8GB RAM, 20GB disk size).
You can choose any flavor available in your OpenStack project or from [the official list](/optimist/specs/flavor_specification/). Ensure that the selected flavor:
  * Has at least 2 cores and 2GB RAM.
  * Is not a Windows image.
* **Custom Root Disk**: The default root disk size is 20GB.
If a larger or faster disk is required, you can configure the Control Plane accordingly. More details can be found [here](/managedk8s/clusterlifecycle/rootdisk/).
* **Custom OIDC**: You may add a custom OIDC endpoint to the Control Plane to configure user access.
Details are available  [here](/managedk8s/clusterlifecycle/oidc/).

### Machine Deployments, Worker Nodes, and Autoscaling
For more information on machine deployments and worker nodes, visit:
[Machine Deployments Documentation](/managedk8s/clusterlifecycle/machinedeployments/)

For details on cluster autoscaling, refer to:
[Cluster Autoscaler Documentation](/managedk8s/clusterlifecycle/autoscaling/)