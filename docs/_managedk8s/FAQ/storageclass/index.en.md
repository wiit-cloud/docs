---
title: StorageClass Setup
lang: "en"
permalink: /managedk8s/storageclasses/
nav_order: 4300
parent: FAQ
---
# StorageClass Setup

We provide one default storage class per cluster.
> **Caution:**
> This is managed by WIIT and can be **overwritten at any time**. Please create a separate storage class for your changes.

```
kubectl get storageclasses.storage.k8s.io
NAME                   PROVISIONER                AGE
cinder-csi (default)   cinder.csi.openstack.org   6h45m
```

## OpenStack Volume Types

The OpenStack volume types sorted by maximum possible IOPS:

* standard <- used in the default class
* high-iops

## Volume Features

We don't provide `Read-Write-Many` volumes. All volumes are `Read-Write-Once`!

## Adding Your Own Classes

If you need to use another type, you can add your own definitions.

Example:

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: my-high-iops-class
provisioner: cinder.csi.openstack.org
volumeBindingMode: WaitForFirstConsumer
parameters:
  type: high-iops
```

Apply with `kubectl apply -f storage-class.yaml`.

* `name`: Choose a unique name so that it does not conflict with the default class.
* `provisioner`: Use your cluster's provisioner. You can always check the default class to verify the right provisioner.
* `type`: Use one of the [officially provided types](/openstack/specs/volume_specification/#volume-types) from the OpenStack platform (at the time of writing standard and high-iops).
* `volumeBindingMode`: There are two options. `WaitForFirstConsumer` is recommended, because if you use more than one availability zone in combination with pod placement policies, `Immediate` can lead to unpredictable scheduling issues.
  * `WaitForFirstConsumer`: The volume is created when a pod requests it.
  * `Immediate`: The volume is created at the moment the PVC is created.

To use the new storage class, you need to change your volume definitions and add the new StorageClass name.
