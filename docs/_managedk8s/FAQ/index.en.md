---
title: FAQ
lang: "en"
permalink: /managedk8s/faq/
nav_order: 4000
has_children: true
---
# Frequently Asked Questions

## What's the recommanded cluster size?
For high availability and fault tolerance, a common recommendation is to have:
- 3 control plane Nodes. With the Flavor s1.medium (4 vCPU / 8 GB RAM).
- 3 Worker Nodes: Having at least two worker nodes per machine deployment helps distribute the workload and provides redundancy. If one worker node fails, the other can continue to run the applications, minimizing downtime.

The recommended approach for machine deployment is to create separate machine deployments in each Availability Zones (AZs). This helps ensure that your application remains available even if one or more AZs experience failures.

For example, create three machine deployments:
 * Machine Deployment in ix1
 * Machine Deployment in ix2
 * Machine Deployment in es1

For each machine deployment, you should configure at least 2 replicas. This ensures that there is redundancy within each AZ.


## What can i configure where
In general you can:
* set the flavor per Machine Deployment
* set the replicas per Machine Deployment
* set the AZ's per Machine Deployment

General your final VM count is based on replicas, Machine Deployments and Availability Zones. The Replica count can be different per Machine Deployment. For example 
* if you have one Machine Deployment in all 3 AZ's with a replica count of 3 for your normal workload you end up with: 1 (Machine Deployment) * 3 (AZ's) * 3 (replicas) = 9 Nodes.
* if you have one Machine Deployment in all 3 AZ's with a replica count of 2 for your infrastructure tools you end up with: 1 (Machine Deployment) * 3 (AZ's) * 2 (replicas) = 6 Nodes.

This means the VM count is a product of an multiplication and therefore if you use more than one AZ (what is higly adviced) you can't have something like 5 VM's.


## What is Cluster Autoscaler?
Cluster Autoscaler is a standalone program that adjusts the size of a Kubernetes cluster to meet the current needs.

## When does Cluster Autoscaler change the size of a cluster?
Cluster Autoscaler increases the size of the cluster when:
  - there are pods that failed to schedule on any of the current nodes due to insufficient resources.
  - adding a node similar to the nodes currently present in the cluster would help.

Cluster Autoscaler decreases the size of the cluster when some nodes are consistently unneeded for a significant amount of time. A node is unneeded when it has low utilization and all of its important pods can be moved elsewhere.

## Can We Reserve a Specific IP for a Kubernetes Service of Type LoadBalancer?
Yes, you can use the `loadbalancer.openstack.org/keep-floatingip` annotation to ensure that the floating IP remains associated with your project and is reused by the Kubernetes service.

Here's an example of how to configure a Kubernetes service with a reserved IP address using the annotation:
```bash
apiVersion: v1
kind: Service
metadata:
  name: nginx-internet
  annotations:
    loadbalancer.openstack.org/keep-floatingip: "true"  # Annotation to keep the floating IP in the project
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
  loadBalancerIP: 45.94.08.9 # Specific floating IP to be reserved for the service
```

## Can We Create a Kubernetes Service with a Specific Floating IP?
Yes, you can create a Kubernetes Service that uses a specific floating IP by setting the loadBalancerIP field in the service definition. The IP you specify as loadBalancerIP must already be allocated as a floating IP in OpenStack.

Here's an example of how you can specify a floating IP for a Kubernetes Service:
```bash 
apiVersion: v1
kind: Service
metadata:
  name: nginx-internet
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  loadBalancerIP: 45.94.08.9 # Specific floating IP to be reserved for the service
```
By using the loadBalancerIP field, you ensure that the service will use the specified floating IP when a load balancer is provisioned.

## Can I use external-dns and openstack designate for automatic dns?

Yes, a few installation and configuration steps are necessary for this which are explained [here](/managedk8s/faq/automatic_dns).
