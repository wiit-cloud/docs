---
title: OIDC
lang: "en"
permalink: /managedk8s/faq/
nav_order: 3350
parent: Managed k8s
---
# Frequently Asked Questions
## What's the recommanded cluster size?
For high availability and fault tolerance, a common recommendation is to have:
- 3 control plane Nodes.
- 3 Worker Nodes: Having at least two worker nodes per machine deployment helps distribute the workload and provides redundancy. If one worker node fails, the other can continue to run the applications, minimizing downtime.

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
  loadBalancerIP: 122.112.219.229  # Specific floating IP to be reserved for the service
```

## Can We Create a Kubernetes Service with a Specific Floating IP?
Yes, you can create a Kubernetes Service that uses a specific floating IP by setting the loadBalancerIP field in the service definition. This configuration is particularly useful when you want to use a pre-allocated static IP address for your service.

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
  loadBalancerIP: 122.112.219.229 # Specific floating IP to be reserved for the service
```
By using the loadBalancerIP field, you ensure that the service will use the specified floating IP when a load balancer is provisioned.
