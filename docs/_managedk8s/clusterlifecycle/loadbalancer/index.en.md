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
- Only Layer 4 (TCP/UDP) is supported - Layer 7 features such as host/path-based routing, TLS termination or header manipulation are not available.
- The Prometheus metrics endpoint (the `PROMETHEUS` listener protocol) is not supported.
- The [API server allowlist](/managedk8s/clusterlifecycle/apiallowlist/) is not supported; requests combining both are rejected.

For a general introduction to Octavia and the `openstack` CLI, see [Octavia Loadbalancers](/openstack/networking/octavia_loadbalancer/#using-the-ovn-load-balancer-driver).

## Source IP preservation

Unlike `amphora`, which source-NATs traffic through the amphora instance, `ovn` forwards traffic to your pods with the original client IP address preserved end-to-end. Our platform automatically manages the security group rules this requires, so `type: LoadBalancer` Services work without any manual configuration on your side.

## Load balancer options

The [OpenStack cloud controller manager](https://github.com/kubernetes/cloud-provider-openstack/blob/master/docs/openstack-cloud-controller-manager/using-openstack-cloud-controller-manager.md#load-balancer) creates and manages the Octavia load balancers for your `type: LoadBalancer` Services. Its behavior is controlled by a set of cluster-wide options. The cloud controller manager is managed by WIIT, so these options can't be changed from inside your cluster.

### Requesting options

To enable or change one or more options, open a ticket through one of our [support channels](/managedk8s/about/support/) and include the following specification. List only the options you want to change; every option you leave out keeps its default.

```yaml
load_balancer_options:
  # by default: ROUND_ROBIN
  lb-method: LEAST_CONNECTIONS
  # by default: true
  create-monitor: true
  # by default: 10s
  monitor-delay: 10s
  # by default: 3s
  monitor-timeout: 3s
  # by default: 3
  monitor-max-retries: 3
  # by default: 3
  monitor-max-retries-down: 3
  # by default: false
  internal-lb: false
  # by default: false
  enable-ingress-hostname: false
  # by default: 10
  max-shared-lb: 10
```

The options can be requested at [cluster creation](/managedk8s/clusterlifecycle/clustercreation/) or as a [cluster change](/managedk8s/clusterlifecycle/clusterchanges/) for an existing cluster. We will apply the requested options or reach out for more details if needed.

### Per-Service annotations

You don't need a ticket if a setting should only apply to some Services. Most options can be set on a single Service with an annotation, which you manage yourself. An annotation takes precedence over the cluster-wide option, except for `internal-lb: true` (see [`internal-lb`](#internal-lb)).

| Cluster-wide option | Service annotation |
| --- | --- |
| `lb-method` | `loadbalancer.openstack.org/lb-method` |
| `create-monitor` | `loadbalancer.openstack.org/enable-health-monitor` |
| `monitor-delay` | `loadbalancer.openstack.org/health-monitor-delay` |
| `monitor-timeout` | `loadbalancer.openstack.org/health-monitor-timeout` |
| `monitor-max-retries` | `loadbalancer.openstack.org/health-monitor-max-retries` |
| `monitor-max-retries-down` | `loadbalancer.openstack.org/health-monitor-max-retries-down` |
| `internal-lb` | `service.beta.kubernetes.io/openstack-internal-load-balancer` |
| `enable-ingress-hostname` | — (cluster-wide only) |
| `max-shared-lb` | — (cluster-wide only) |

For example, to use `LEAST_CONNECTIONS` with a faster health check for one Service:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app
  annotations:
    loadbalancer.openstack.org/lb-method: LEAST_CONNECTIONS
    # in seconds, without unit
    loadbalancer.openstack.org/health-monitor-delay: "5"
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
    - port: 443
      targetPort: 8443
```

There are further annotations without a cluster-wide option, for example to keep the floating IP when the Service is deleted (`loadbalancer.openstack.org/keep-floatingip`), enable the PROXY protocol (`loadbalancer.openstack.org/proxy-protocol`) or set listener timeouts (`loadbalancer.openstack.org/timeout-*`).

For the full list and the values each annotation accepts, see the official OCCM documentation: [Service annotations](https://github.com/kubernetes/cloud-provider-openstack/blob/master/docs/openstack-cloud-controller-manager/expose-applications-using-loadbalancer-type-service.md#service-annotations).

### Option reference

#### `lb-method`

The algorithm used to distribute connections across your nodes.

- `ROUND_ROBIN` (default) - connections are distributed evenly in turn.
- `LEAST_CONNECTIONS` - each new connection goes to the backend with the fewest active connections.
- `SOURCE_IP` - connections from the same client IP address always go to the same backend.
- `SOURCE_IP_PORT` - connections from the same client IP address and port always go to the same backend.

The `ovn` provider only supports `SOURCE_IP_PORT`.

#### `create-monitor`

Creates an Octavia health monitor for each load balancer pool (`true` or `false`, default `true`). With a health monitor, Octavia stops sending traffic to nodes that don't respond and resumes once they are healthy again. Without one, traffic can be sent to an unreachable node until Kubernetes removes it from the pool.

Keep the health monitor enabled if your Services use `externalTrafficPolicy: Local`, because then only nodes running a pod of the Service can answer.

#### `monitor-delay`

The interval between two health checks, as a duration with unit, for example `10s` (default `10s`). Only used when `create-monitor` is `true`.

#### `monitor-timeout`

How long a health check waits for a reply before it counts as failed, as a duration with unit, for example `3s` (default `3s`). Must be lower than `monitor-delay`. Only used when `create-monitor` is `true`.

#### `monitor-max-retries`

The number of successful checks in a row needed before a node is marked healthy again, between 1 and 10 (default `3`). Only used when `create-monitor` is `true`.

#### `monitor-max-retries-down`

The number of failed checks in a row after which a node is marked unhealthy and receives no more traffic, between 1 and 10 (default `3`). Only used when `create-monitor` is `true`.

#### `internal-lb`

When `true`, load balancers receive only a private IP address in your cluster network and no floating IP, so they aren't reachable from the internet (default `false`). Use this if your Services should only be reachable from within your OpenStack project or through a VPN.

With `internal-lb: true`, every load balancer in the cluster is internal; the `service.beta.kubernetes.io/openstack-internal-load-balancer` annotation is ignored. With the default `false`, you can still make a single Service internal by setting that annotation to `"true"`.

#### `max-shared-lb`

The maximum number of Services that can share one Octavia load balancer through the `loadbalancer.openstack.org/load-balancer-id` annotation, between 1 and 999 (default `10`). Sharing load balancers reduces the number of load balancers and floating IPs you need.

### Things to keep in mind

- The options apply to all `type: LoadBalancer` Services in the cluster.
- Options that aren't listed above, such as the networks and subnets used for load balancers, are managed by WIIT and can't be changed.
