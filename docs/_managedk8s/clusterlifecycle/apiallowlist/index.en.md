---
title: API Server Allowlist
lang: "en"
permalink: /managedk8s/clusterlifecycle/apiallowlist/
nav_order: 3347
parent: Cluster Lifecycle
---
# API Server Allowlist

By default, the Kubernetes API of your cluster is reachable from any IP address and protected by authentication only.
With the API server allowlist you can additionally restrict network access to the Kubernetes API to a list of IP ranges (CIDRs) you trust.

Only the Kubernetes API is affected. Your applications and their `type: LoadBalancer` Services stay reachable as before.

{: .warning }
The API server allowlist cannot be combined with the `ovn` [load balancer provider](/managedk8s/clusterlifecycle/loadbalancer/). Requests that set both `api_server_allowed_cidrs` and `load_balancer_provider: ovn` are rejected. Use the default provider (`amphora`) if you need the allowlist.

## Requesting the allowlist

Add the allowed IP ranges to your [cluster creation](/managedk8s/clusterlifecycle/clustercreation/) request, or request them as a [cluster change](/managedk8s/clusterlifecycle/clusterchanges/) for an existing cluster:

```yaml
# by default: empty (API reachable from everywhere)
api_server_allowed_cidrs:
  - w.x.y.z/24   # office
  - a.b.c.d/32   # VPN gateway
```

- Only IPv4 ranges are supported.
- Use CIDR notation; a single address is written as `/32`.
- An empty list disables the allowlist again.

## What we need from you

List every public IP range from which the Kubernetes API is accessed, for example:

- office networks and VPN gateways of your administrators and developers
- CI/CD runners deploying to the cluster
- external tools that talk to the Kubernetes API (GitOps, backup, monitoring, ...)

Use the public IP address the traffic leaves your network with (the NAT/egress address), not internal addresses.

Workloads inside the cluster that use the Kubernetes API (e.g. operators or controllers) do not need an entry.

## Addresses added by WIIT

In addition to your ranges, we automatically add a few addresses:

- addresses the cluster itself needs to work (its own network and router)
- the WIIT addresses used to operate, monitor and support your cluster:

| Address            | Used for                          |
| ------------------ | --------------------------------- |
| `62.141.47.6/32`   | WIIT operations staff             |
| `89.163.172.24/32` | WIIT cluster management           |
| `89.163.172.39/32` | WIIT platform services            |

The allowlist is applied to the listener of the Kubernetes API load balancer in your OpenStack project, not to a security group.
You will see these addresses next to your own ranges in the listener's allowed CIDRs:

```bash
# the listener name ends with "-kubeapi-6443"
openstack loadbalancer listener list -c id -c name
openstack loadbalancer listener show <listener-id> -c allowed_cidrs
```

**Please do not remove or change them** - without them we cannot manage your cluster, and updates or support may fail.
Changes made directly on the listener are reset to the requested list automatically.
If you have questions about an address, contact [support](/managedk8s/about/support/).

## Things to keep in mind

- Requests from addresses that are not on the allowlist are dropped; `kubectl` simply times out instead of returning an error.
- Keep the list up to date when your office, VPN or CI/CD addresses change, otherwise you lock yourself out. We can still access the cluster and update the list on your request.
