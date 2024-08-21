---
title: Cluster Creation
lang: "en"
permalink: /managedk8s/clusterlifecycle/clustercreation/
nav_order: 3100
has_children: true
parent: Cluster Lifecycle
---
This section of the documentation describes what needs to be provided so that we successfully create a cluster for you.
The following requirement values should be provided before the cluster creation. You may also add the optional values if you
need customized features in your cluster such as using custom root disk or enabling autoscaler, otherwise they will get the
default values, which are described below.


## Prerequisites

### Openstack credentials

You should have an existing or newly created openstack tenant. You should create in your openstack tenant and provide the following:

* [Application credentials](/managedk8s/clusterlifecycle/appcredentials/)

bounded to the project where you want the new cluster to be created. 

### Required information for the new cluster

* Cluster name - it should have a maximum of 22 characters

* Customer ID - defaults to the company id number if not specified for customer

* Application Creds - as Discussed in prerequisites


## Optional requirements or configurable features with a sane default

# K8S version
If not specified, cluster will be deployed in the latest supported kubernetes minor version.

For more infromation about supported versions and Deprecations/EOL or other concern regarding the versions [look here](managedk8s/about/kubernetesverions/)


## Optional requirements
TODO: create docu for root disks and link here where needed

### ControlPlane values
TODO: add that the vm are shown in there tenent
TODO: link to openstack flavor list docu
TODO: state tat we habe a 3 AZ HA CP

* Flavor - default flavor type s1.medium (`4 cores`, `8GB RAM` and `20GB` disk size)
  You can select each flavor that you can see in your OpenStack project, but make sure the flavor:
  * has at least 2 cores and 2 GB RAM
  * is not a windows image
* Custom root disk size and/or volume type - defaults are the flavor standard limits (volume type options: `low-iops`, `high-iops`, `default`)

### Worker nodes values
TODO: add workernode docu with a lot more details -> multiaz howto, roles/restrictuions and link disk again

* Provide the machineDeployment name (in case of multiple ones), the format of the machineDeployment name will be `md-name-az-md` for example `md-autoscaler-test-ix1-md`.
  If the md-name is not provided, the format of the machineDeployment name will be `cluster-name-az-md` for example `cluster-test-ix2-md`.
* In the generated values.yaml will be an entry for each machineDeployment which can be configured individually. The defaults are:
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
* Flavor/Machine Type - default type is s1.large (`8 cores`, `16GB RAM` and `20GB` disk size)
  You can select each flavor that you can see in your OpenStack project, but make sure the flavor:
  * has at least 2 cores and 2 GB RAM
  * is not a windows image
* Number of replicas - integer value, the default is 3 nodes
* Availability zone (AZ) - if preferred, specify the AZ from `ix1`, `ix2` or `es1` available zones. The default value will be a random AZ.
  If there are multiple AZs configured, there will be a machineDeployment created for each AZ with the specified replica count. For example if you want to use 3 replicas with AZs ix1 and ix2, there will be 2 machineDeployments each with 3 replicas.
* Custom root disk size and/or volume type - if not enabled, defaults are the flavor limits (volume type options: `low-iops`, `high-iops`, `default`)
* roles are set as labels on the nodes. where the labels have the following format: `node-role.kubernetes.io/<ROLENAME>: ""`
* restrictions are set as labels on the nodes. where the labels have the following format: `node-role.kubernetes.io/<RESTRICTIONNAME>: ""`

### Cluster autoscaler  (the same for each machineDeployment if more than one)
TODO: create extra topic for autoscaler and add here

* Provide the machineDeployment name, the format of the machineDeployment name will be `md-name-az-md` for example `md-autoscaler-test-ix1-md`.
  If the md-name is not provided, the format of the machineDeployment name will be `cluster-name-az-md` for example `cluster-test-es1-md`
* Enable - autoscaler is not enabled by default
* If enabled, provide the desired `min` and `max` number of worker nodes


### custom oidc
TODO: add docu vor oidc
You can add a custom oidc configuration in the values.yaml for the cluster. This can only be done in the cluster creation process.

```yaml
oidc_ca_file: "path_to_file"
oidc_client_id: "12345"
oidc_groups_claim: "email"
oidc_groups_prefix: "oidc:"
oidc_issuer_url: "https://..."
oidc_required_claims:
- 'key=value'
oidc_signing_algs: "RS256"
oidc_username_claim: "sub"
oidc_username_prefix: "..."
```

The global defaults are filled with the configuration for this gitlab.

### Maintenance Window
TODO: remove for offical docs, as we need more time to see if it really works
TODO: add a flacar update policie/effect docu

We support Maintenance windows for Clusters on cron style syntax. The default is `* * * * *`
You can provide us with a more suitable window.

Try to honor this windows for all automatic updates. At the moment that includes flatcar image updates and updates of base components installed in the cluster.


## How to request for a new Cluster
TODO: move channel discusson to support topic

* Please write a message in our Slack channel [#product_gks](https://app.slack.com/client/T3Q3RTXQF/C8FJLPPEU) with:
  * your email address
  * your team name
  * the values set below according to your needs

Copy the block below and provide all the desired values for your new cluster. For the Openstack credentials, please provide a
bitwarden link we can access where we you have saved the credentials.
You can update the values list by removing or leaving the optional values empty if you don't need something customized from the
default values.

```bash
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
