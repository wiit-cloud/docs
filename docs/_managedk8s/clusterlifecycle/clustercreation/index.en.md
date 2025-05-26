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
After the cluster is created, you will receive an admin kubeconfig through a secure method, along with a [WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name) for later support queries.

**For further communication we need the [WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name) to identify your cluster.**

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


### Machine Deployments, Worker Nodes and Autoscaling
For Machine Deployment look into the more detailed [docs](/managedk8s/clusterlifecycle/machinedeployments/)

As ther are a some more options. 

Default will get you a single Machine Deployment with 3 Nodes on 1 AZ

#### Cluster autoscaler  (the same for each machineDeployment if more than one)
We support the Cluster Autoscalar which we can activate seperatly for each Machine Deploment.

Details can be found [here](/managedk8s/clusterlifecycle/autoscaling/)
