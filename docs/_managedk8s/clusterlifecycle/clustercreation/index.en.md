---
title: Cluster Creation
lang: "en"
permalink: /managedk8s/clusterlifecycle/clustercreation/
nav_order: 3100
parent: Cluster Lifecycle
---
# Cluster creation
To successfully create a cluster, please provide the required information outlined below. Ensure all details are filled in correctly before proceeding with the cluster creation.
Copy the block below and complete it with the necessary information for your new cluster:
```
# required information
cluster_name: 
customer_id: 

# Kubernetes version
# by default the latest supported k8s minor version provided by us
kubernetes_version: 

# control plane flavor
# by default s1.medium
flavor: 

## Machine deployments: one block per MD (default: 1 md, 3 replicas, s1.large, random AZ)
# machine deployment name
# by default clusterName-az-md
md_name:  
# replicas by default 3
replicas:
# by default s1.large
flavor: 
# by default random-az
availability_zone: 
# supported values: az1, az2, az3, sz1 (sz1 may not be combined with other AZs)
# warning: sz1 is a special stretched zone, see https://docs.wiit-cloud.io/openstack/intro/#stretched-zone-sz1

## add the autoscaler (default: disabled)
min_size:
max_size:

## restrict Kubernetes API access to these CIDRs (default: open to everyone)
api_server_allowed_cidrs:
```
Detailed information about options can be found below.

## Optional Settings

The IP ranges used for pod addresses and service addresses can be adjusted.
If not provided, we will use the defaults shown below.
Note that these should only be changed if they clash with an externally routed network.

```
# the IP range for pod allocation
pod_cidr: 172.25.0.0/16

# the IP range for service allocation
service_cidr: 10.96.0.0/12

# the CoreDNS address is the 10th address of the service CIDR
dns_service_address: 10.96.0.10
```

## What You Will Receive
After the cluster is created, you will receive an admin kubeconfig through a secure method, along with a [WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name) for later support queries.

**For further communication we need the [WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name) to identify your cluster.**

# Detailed Information

## Prerequisites
### OpenStack Credentials
You must have an existing or newly created OpenStack tenant. In this tenant, create and provide the following:
- [Application credentials](/managedk8s/clusterlifecycle/appcredentials/) linked to the project where you want the new cluster to be created.


### Required Information for the New Cluster
* Cluster Name: A maximum of 22 characters.
* Customer ID: Defaults to the company ID number if not specified.
* Application Credentials: As mentioned in the prerequisites.

### Optional Requirements and Configurable Features (with Default Values)
#### Kubernetes Version
If not specified, the cluster will be deployed with the latest supported Kubernetes minor version.
We support manually setting the major.minor version (e.g. 1.33); it then needs to be updated manually if a new version is needed.
The patch level is updated by us and deployed automatically.

For more details on supported versions, deprecations, EOL, or other version concerns, [click here](/managedk8s/about/kubernetesversions/).

#### OpenStack Network
Provide an existing network ID, or we will create a new network for you.
Provided networks will not be deleted when the cluster is deleted. They can be reused after cluster deletion.
Automatically created ones will be deleted with cluster deletion.

If you have no special requirements, use an automatically created network. This way, no separate creation or cleanup process is needed on our side.
For requirements like shared networks, added VPNs, etc., please use the provided options.

#### Load Balancer Provider
`type: LoadBalancer` Services are backed by OpenStack Octavia. By default, the `amphora` provider is used; the `ovn` provider is also available.

Details can be found [here](/managedk8s/clusterlifecycle/loadbalancer/).

#### API Server Allowlist
By default, the Kubernetes API is reachable from everywhere. You can restrict it to a list of IP ranges.
Not available together with the `ovn` load balancer provider.

Details can be found [here](/managedk8s/clusterlifecycle/apiallowlist/).

### Machine Deployments, Worker Nodes and Autoscaling
For Machine Deployments, look into the more detailed [docs](/managedk8s/clusterlifecycle/machinedeployments/), as there are some more options.

By default, you get a single machine deployment with 3 nodes in one AZ.

#### Cluster autoscaler (the same for each machineDeployment if more than one)
We support the Cluster Autoscaler which we can activate separately for each Machine Deployment.

Details can be found [here](/managedk8s/clusterlifecycle/autoscaling/).
