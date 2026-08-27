---
title: Allocatable resources
lang: "en"
permalink: /managedk8s/clusterlifecycle/allocatable-resources/
nav_order: 3530
parent: Cluster Lifecycle
---
# Allocatable resources

The CPU and memory shown by an OpenStack flavor describe the capacity of the virtual machine. They do not represent the capacity available for your applications. Every worker node needs capacity for the operating system, Kubernetes itself, and the managed platform components that run in your cluster.

## From flavor capacity to workload capacity

For worker nodes, Kubernetes accounts for the flavor capacity as follows:

- **`systemReserved`** is set aside for the operating system and system services.
- **`kubeReserved`** is set aside for Kubernetes node components and the container runtime. Its value is calculated for each flavor.
- **Node allocatable** is the remaining capacity that Kubernetes can schedule pods onto. The memory calculation also includes a small eviction reserve so that the node can react to memory pressure safely.

The reservations are applied to the node before your application pods are scheduled. Their values depend on the selected flavor; they are not a fixed percentage of every node.

### Examples: a small, a large, and an extra-large worker node

The current reservation policy produces the following values for three worker flavors:

| Capacity layer | `s1.small` CPU | `s1.small` memory | `s1.large` CPU | `s1.large` memory | `s1.2xlarge` CPU | `s1.2xlarge` memory |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| OpenStack flavor capacity | 2,000m (2 vCPU) | 4,096Mi (4 GiB) | 8,000m (8 vCPU) | 16,384Mi (16 GiB) | 30,000m (30 vCPU) | 65,536Mi (64 GiB) |
| `systemReserved` | 80m | 256Mi | 80m | 256Mi | 80m | 256Mi |
| `kubeReserved` | 20m | 1,023Mi | 20m | 2,661Mi | 65m | 5,610Mi |
| Memory eviction reserve | — | 100Mi | — | 100Mi | — | 100Mi |
| **Node allocatable** | **1,900m** | **2,717Mi** | **7,900m** | **13,367Mi** | **29,855m** | **59,570Mi** |

Although `s1.large` has more vCPUs than `s1.small`, both reserve 100m of CPU. The tiered reservation target is the value that scales with the node size: it is 70m for `s1.small` and 90m for `s1.large`. To split this target into `systemReserved` and `kubeReserved`, the fixed 80m system baseline is subtracted: `70m - 80m = -10m` and `90m - 80m = 10m`. Because both remainders are below the 20m minimum, `kubeReserved` is set to 20m in both cases, resulting in `80m + 20m = 100m` total reserved CPU. On `s1.2xlarge`, the target reaches 145m, so `kubeReserved` increases to 65m. The resulting allocatable memory is 2,717Mi, 13,367Mi, and 59,570Mi for `s1.small`, `s1.large`, and `s1.2xlarge`, respectively, before platform and application pod requests.

The absolute reservation can increase with the size of the node, but its proportion decreases. For `s1.small`, the system, Kubernetes, and eviction reservations account for 1,379Mi (33.7%) of memory and 100m (5%) of CPU. For `s1.large`, they account for 3,017Mi (18.4%) of memory and 100m (1.25%) of CPU. The exact values depend on the flavor and may change when the reservation policy is updated.

## Platform components also consume node capacity

A managed Kubernetes cluster needs platform components for networking, DNS, OpenStack integration, storage, metrics, and backups. By default, your cluster runs components such as Calico, CoreDNS, the OpenStack cloud controller manager, Cinder CSI, `kube-proxy`, Metrics Server, NodeLocal DNS, and backup and defragmentation jobs. These components are managed by WIIT and are not part of your workloads. GPU discovery is optional and is only relevant for GPU worker nodes; the exact set of components can vary with your cluster configuration.

These platform components declare resource requests and, where appropriate, resource limits. Requests are used for scheduling and therefore reduce the capacity available to your workloads. Limits cap runtime usage but do not reserve additional capacity for scheduling. DaemonSet, Deployment, and Job requests apply to the nodes where their pods run; a DaemonSet normally places one pod on each eligible node. The capacity available for your application pods is therefore the node's allocatable capacity minus the requests of the platform pods scheduled on that node and the requests of your other workloads.

Read more: https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits

## Impact on workloads and autoscaling

Plan worker capacity using node allocatable resources rather than the raw OpenStack flavor size. Define realistic CPU and memory requests for your application containers so that the scheduler and the [Cluster Autoscaler](/managedk8s/clusterlifecycle/autoscaling/) can make decisions based on the resources your workloads actually need.

The Cluster Autoscaler also uses the calculated allocatable capacity when scaling a worker group from zero. It does not assume that a new node can offer the full CPU and memory of its flavor. For more information, see [Cluster Autoscaler](/managedk8s/clusterlifecycle/autoscaling/).
