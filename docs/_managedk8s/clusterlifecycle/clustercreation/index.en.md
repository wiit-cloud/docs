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

## How to request for a new Cluster

Copy the block below and provide all the desired values for your new cluster. For the Openstack credentials, please provide a
bitwarden link we can access where we you have saved the credentials.
You can update the values list by removing or leaving the optional values empty if you don't need something customized from the
default values.

```
# required values
cluster_name:
customer_id:
# optional values
k8s_version:
## controlplane
flavor:
use_custom_disk:
disk_size:
volume_type:
## workers
md_name:
replicas:
flavor:
use_custom_disk:
disk_size:
volume_type:
availability_zone:
## cluster autoscaler
md_name:
use_autoscaler:
min_size:
max_size:
```

## Conclusion

All the above steps should enable us to create capi cluster for you in your OpenStack tenant.

We will notify you when it is created, and you will receive the kubeconfig of the newly created
cluster and you will be able to set the KUBECONFIG:

```bash
export KUBECONFIG=$(pwd)/customer-kubeconfig
```
TODO: add that they need to have the clusterid/clustername pair, for further requests
