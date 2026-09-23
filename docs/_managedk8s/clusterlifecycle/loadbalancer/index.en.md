---
title: Load Balancer
lang: "en"
permalink: /managedk8s/clusterlifecycle/loadbalancer/
nav_order: 3345
parent: Cluster Lifecycle
---
# Load Balancer

Workload clusters use [OpenStack Octavia](/openstack/networking/octavia_loadbalancer/) to implement Kubernetes `type: LoadBalancer` Services. Two Octavia providers are available:

- **`amphora`** (default) - Octavia spawns a dedicated virtual machine (amphora) running HAProxy for each load balancer.
- **`ovn`** - Load balancing is implemented natively by OVN, without dedicated virtual machines.

## Choosing a provider

The provider is set once per cluster at [cluster creation](/managedk8s/clusterlifecycle/clustercreation/) and applies to every `type: LoadBalancer` Service created in that cluster. To request the `ovn` provider, add the following to your cluster creation request:

```yaml
# by default: amphora
load_balancer_provider: ovn
```

If you don't have a specific reason to change it, keep the default (`amphora`).

## Differences from `amphora`

- No dedicated amphora virtual machines are created, so load balancers provision faster and use fewer OpenStack resources.
- Only Layer 4 (TCP/UDP) is supported - Layer 7 features such as host/path based routing, TLS termination or header manipulation are not available.
- The Prometheus metrics endpoint (the `PROMETHEUS` listener protocol) is not supported.

For a general introduction to Octavia and the `openstack` CLI, see [Octavia Loadbalancers](/openstack/networking/octavia_loadbalancer/#using-the-ovn-load-balancer-driver).

## Source IP preservation

Unlike `amphora`, which source-NATs traffic through the amphora instance, `ovn` forwards traffic to your pods with the original client IP address preserved end-to-end. Our platform automatically manages the security group rules this requires, so `type: LoadBalancer` Services work without any manual configuration on your side.
