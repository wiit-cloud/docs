---
title: Component Extra Args
lang: "en"
permalink: /managedk8s/clusterlifecycle/extraargs/
nav_order: 3360
parent: Cluster Lifecycle
---
# Component Extra Args

You can pass extra command-line flags to several cluster components. Use this to enable
behaviour that has no dedicated setting, for example extra audit logging on the API server
or workload-identity issuer flags.

Open a [support request](/managedk8s/about/support/) that lists the component, the flag name,
and the flag value. We add the flags to your cluster.

## Components that accept extra args

| Component                       | What it controls                                                                                                                                                                                                                         |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `kube-apiserver`                | The cluster API server. Also where OIDC login is configured (see [OIDC](/managedk8s/clusterlifecycle/oidc/)).                                                                                                                            |
| `kube-controller-manager`       | Core control loops (e.g. garbage collection).                                                                                                                                                                                            |
| `kube-scheduler`                | Pod scheduling.                                                                                                                                                                                                                          |
| `etcd`                          | The cluster datastore. Wrong flags here can corrupt data — request with care.                                                                                                                                                            |
| `kubelet` (control plane nodes) | The kubelet on control plane nodes only. Worker-node kubelet flags are not configurable.                                                                                                                                                 |
| `cluster-autoscaler`            | Flags for the single cluster-wide autoscaler process. These apply to every machine deployment. Per machine deployment settings (enable, min, max, scale-down tuning) live with [autoscaling](/managedk8s/clusterlifecycle/autoscaling/). |

A value you set overrides our default for that flag.

## Reserved flags

A few flags are managed by us and cannot be set. A request that includes one is rejected.

| Component                 | Reserved flags                                                                                       |
|---------------------------|------------------------------------------------------------------------------------------------------|
| `kube-apiserver`          | none                                                                                                 |
| `kube-controller-manager` | `flex-volume-plugin-dir`                                                                             |
| `kube-scheduler`          | none                                                                                                 |
| `etcd`                    | none                                                                                                 |
| `kubelet`                 | `cloud-provider`                                                                                     |
| `cluster-autoscaler`      | `cloud-provider`, `kubeconfig`, `clusterapi-cloud-config-authoritative`, `node-group-auto-discovery` |

## Responsibility

Extra args change low-level component behaviour. A wrong flag can break your cluster,
including the control plane, which we cannot always recover automatically. You own the flags
you request. We may decline a flag that endangers platform operations or compliance.

## How to request

Open a [support request](/managedk8s/about/support/) with your
[WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name), and for each component the
flags and values you want.

We apply the flags and roll the affected components, or reach out if a flag is reserved or
looks risky.
