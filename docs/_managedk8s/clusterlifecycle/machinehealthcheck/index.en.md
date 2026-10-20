---
title: Machine Health Check
lang: "en"
permalink: /managedk8s/clusterlifecycle/machinehealthcheck/
nav_order: 3505
parent: Cluster Lifecycle
---
# Machine Health Check

A Machine Health Check automatically detects unhealthy worker nodes in your cluster and replaces them. Instead of waiting for someone to notice a broken node and fix it by hand, the platform deletes the faulty machine and the machine deployment creates a new one with the same configuration (flavor, availability zone, root disk, labels, and taints).

Machine Health Checks are configured per machine deployment and only apply to worker nodes. The control plane is monitored and repaired by us separately.

# How a Machine Health Check Works

**Detection:**
With the default configuration, a machine is considered unhealthy if one of the following conditions persists for **10 minutes**:

- The node reports the `Ready` condition as `False` or `Unknown`, for example because the kubelet stopped responding or the node lost its network connection.
- The machine's `NodeHealthy` condition is `False`. This summarizes the node's readiness and resource pressure.
- The machine's `InfrastructureReady` condition is `False`, for example because the OpenStack instance went into an error state.
- A newly created machine does not join the cluster as a node within 10 minutes.

In addition, a machine whose node has been deleted from the cluster is always considered unhealthy.

The 10-minute timeout prevents nodes that are slower than usual, for example during startup, from being replaced unnecessarily.

**Remediation:**
When a machine is marked as unhealthy, it is deleted and the machine deployment creates a replacement machine. The new node joins the cluster and becomes available for scheduling. Pods that were running on the unhealthy node are rescheduled by Kubernetes onto the remaining healthy nodes.

**Safeguard:**
By default, only **one machine at a time** is replaced per machine deployment. If many nodes become unhealthy at once, for example during a short network disruption, this prevents all machines from being recreated at the same time.

# Enabling the Machine Health Check

The Machine Health Check is not enabled by default. To enable it, or to change its settings, please contact us using the instructions provided [here](/managedk8s/about/support/).

When reaching out, include the following information:

- [WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name)
- The name(s) of the machine deployment(s) the Machine Health Check should apply to
- Whether the default configuration should be used, or which custom checks and remediation settings you need

It is possible to enable or disable the Machine Health Check at any time for one or more machine deployments.

# Default Configuration

If you enable the Machine Health Check without further settings, the following configuration is applied:

```yaml
workers:
- name: <machine-deployment-name>
  healthCheck:
    enabled: true
    checks:
      nodeStartupTimeoutSeconds: 600
      unhealthyNodeConditions:
      - type: Ready
        status: "False"
        timeoutSeconds: 600
      - type: Ready
        status: Unknown
        timeoutSeconds: 600
      unhealthyMachineConditions:
      - type: NodeHealthy
        status: "False"
        timeoutSeconds: 600
      - type: InfrastructureReady
        status: "False"
        timeoutSeconds: 600
    remediation:
      maxInFlight: 1
```

# Custom Configuration

You can replace the default checks and remediation settings with your own. Choose the timeouts with care: values that are too short can cause nodes to be replaced during short, self-recovering disruptions, while values that are too long leave broken nodes in the cluster for longer.

## Checks

The `checks` block defines when a machine is considered unhealthy:

| Option | Controls |
|---|---|
| `nodeStartupTimeoutSeconds` | Maximum time in seconds a new machine may take to join the cluster as a node. Defaults to 10 minutes. Set it to `0` to disable this check. |
| `unhealthyNodeConditions` | List of conditions on the Kubernetes node, for example `Ready`, `MemoryPressure`, `DiskPressure`, or `PIDPressure`. Up to 20 entries. |
| `unhealthyMachineConditions` | List of conditions on the machine, for example `NodeHealthy` or `InfrastructureReady`. Up to 20 entries. |

Each condition consists of a `type`, a `status` (`True`, `False`, or `Unknown`), and a `timeoutSeconds`. A machine is considered unhealthy as soon as **any** of the conditions has been in the given status for at least its timeout. Each condition has its own timeout, so you can, for example, react faster to `Ready=Unknown` than to `DiskPressure=True`.

Independent of the configured checks, a machine whose node has been deleted from the cluster is always considered unhealthy and is replaced immediately.

Example: Replace nodes that are not ready for 5 minutes, or that report disk pressure for 30 minutes:

```yaml
workers:
- name: <machine-deployment-name>
  healthCheck:
    enabled: true
    checks:
      nodeStartupTimeoutSeconds: 600
      unhealthyNodeConditions:
      - type: Ready
        status: "False"
        timeoutSeconds: 300
      - type: Ready
        status: Unknown
        timeoutSeconds: 300
      - type: DiskPressure
        status: "True"
        timeoutSeconds: 1800
```

## Remediation

The `remediation` block defines how many machines are replaced and under which circumstances:

| Option | Controls |
|---|---|
| `maxInFlight` | How many machines can be replaced at the same time, as an absolute number (e.g. `2`) or a percentage of the desired replicas (e.g. `20%`). If not set, the number of parallel replacements is only limited by the number of replicas. |
| `triggerIf.unhealthyLessThanOrEqualTo` | Machines are only replaced if the number of unhealthy machines is less than or equal to this value, as an absolute number (e.g. `2`) or a percentage (e.g. `40%`). If not set, machines are replaced regardless of how many are unhealthy. |
| `triggerIf.unhealthyInRange` | Machines are only replaced if the number of unhealthy machines is within the given range, e.g. `[3-5]`. Takes precedence over `unhealthyLessThanOrEqualTo` if both are set. |

The `triggerIf` options act as a circuit breaker. If too many machines are unhealthy at the same time, this usually indicates a wider problem, such as a network or availability zone outage, where replacing machines would not help. In this case, no machines are replaced until the number of unhealthy machines is back within the configured limit.

How the limits are evaluated:

- **Absolute number:** With `unhealthyLessThanOrEqualTo: 2`, up to 2 unhealthy machines are replaced. If 3 or more machines are unhealthy, none are replaced, regardless of the size of the machine deployment.
- **Percentage:** With `unhealthyLessThanOrEqualTo: 40%` and 25 machines, up to 10 unhealthy machines are replaced; with 6 machines, up to 2. Non-whole results are rounded down. In machine deployments with only a few replicas, a single unhealthy machine already represents a large percentage.
- **Range:** With `unhealthyInRange: "[3-5]"`, machines are only replaced if 3, 4, or 5 machines are unhealthy. With 2 or fewer, or 6 or more unhealthy machines, none are replaced.

Example: Replace at most one machine at a time, and stop replacing machines if more than 40% of the machine deployment is unhealthy:

```yaml
workers:
- name: <machine-deployment-name>
  healthCheck:
    enabled: true
    remediation:
      maxInFlight: 1
      triggerIf:
        unhealthyLessThanOrEqualTo: 40%
```

## Combining Custom Settings with the Defaults

Custom settings are not merged with the defaults. If you specify `checks` or `remediation`, even partially, that block of the default configuration is replaced completely. For example, the remediation example above keeps the default checks, but if it did not set `maxInFlight: 1`, any number of machines could be replaced at the same time. If you only want to change a single value, please specify the complete block, including the defaults you want to keep.

# Checking Node Health

You can see the state of your nodes with kubectl:

```bash
# List all nodes and their status
kubectl get nodes

# Show the conditions of a specific node
kubectl describe node <node-name>
```

A node that is being replaced shows the status `NotReady` until it is removed from the cluster. Shortly after, a new node with a different name appears in the list.

If you have any questions or need further assistance, please [contact us](/managedk8s/about/support/).
