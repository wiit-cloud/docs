---
title: Cluster Creation
lang: "en"
permalink: /managedk8s/clusterlifecycle/clustercreation/
nav_order: 3100
parent: Cluster Lifecycle
---
# Cluster creation
This section of the documentation describes what needs to be provided so that we successfully create a cluster for you.
The following required information should be provided before the cluster creation.

Copy the block below and provide all the needed information for your new cluster.

```
# required infromation
cluster_name
customer_id


# other k8s version (default: latest Minor)
kubernetes_version:

## different controlplane flavor (default: s1.medium)
flavor:

## extra root disk for teh control plane (default: flavor default)
disk_size:
volume_type:

## Machine deployments one block per MD (default: 1 md, 3 replicas, s1.large, random AZ)
md_name:
replicas:
flavor:
availability_zone:

### different root disk (default: flavor default)
disk_size:
volume_type:

## add the autoscaler (default: disabled)
min_size:
max_size:
```
Detailed information about options can be found below.

### What will get back (better line)
After cluster creation you will get a admin kubeconfig via as secure way and a Cluster ID

**For further communication we need the cluster id and cluster name to identify your cluster.**

# Detailed Information

## Prerequisites

### Openstack credentials

You should have an existing or newly created openstack tenant. You should create in your openstack tenant and provide the following:

* [Application credentials](/managedk8s/clusterlifecycle/appcredentials/)

bounded to the project where you want the new cluster to be created. 

## Required information for the new cluster

* Cluster name - it should have a maximum of 22 characters

* Customer ID - defaults to the company id number if not specified for customer

* Application Creds - as Discussed in prerequisites


## Optional requirements or configurable features with a sane default

### K8S version
If not specified, cluster will be deployed in the latest supported kubernetes minor version.

For more information about supported versions and Deprecations/EOL or other concern regarding the versions [look here](/managedk8s/about/kubernetesverions/)

### Openstacknetwork
either provide id or we create a new extra network

### ControlPlane
There are always 3 Control Plane nodes in any cluster, distributed on all AZs for maximum reliability

#### Flavor 
The default flavor is: s1.medium (`4 cores`, `8GB RAM` and `20GB` disk size)

You can select each flavor that you can see in your OpenStack project or mostly all from the [official list](/optimist/specs/flavor_specification/), but make sure the flavor:
  * has at least 2 cores and 2 GB RAM
  * is not a windows image
 
#### Custom root disk 
We use the flavor default for the root disk, which is a 20GB disk

If there is a need for bigger or faster disk, we can configure the Controlplane. Detailed information [here](/managedk8s/clusterlifecycle/rootdisk/)

#### custom oidc
It is possible to add a customer OIDC endpoint to the Control Plane, so it can be used to configure proper access for your users

Details can be found [here](/managedk8s/clusterlifecycle/oidc/)


### Machine Deployments, Worker Nodes and Autoscaling
/managedk8s/clusterlifecycle/machinedeployments/
#### Cluster autoscaler  (the same for each machineDeployment if more than one)
/managedk8s/clusterlifecycle/autoscaling/
