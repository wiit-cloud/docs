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
