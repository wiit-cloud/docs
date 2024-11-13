---
title: StorageClass Setup
lang: "en"
permalink: /managedk8s/storageclasses/
nav_order: 4200
parent: FAQ
---
# StorageClass Setup

We provide one default storage class per Cluster.
> **Caution:**
> This is managed by WIIT and can be **overwritten at any time**. Please create a separate storage class for your changes.

```
kubectl get storageclasses.storage.k8s.io
NAME                   PROVISIONER                AGE
cinder-csi (default)   cinder.csi.openstack.org   6h45m
```

## Openstack Volume Types

The Openstack volume types sorted by maximum possible IOPS:

* low-iops
* default <- used in the default class
* high-iops

## Volume Features

We don't provide `Read-Write-Many` Volumes. All Volumes are `Read-Write-Once`!

## Adding Your Own Classes

If you need use one of the other types, you can add your own definitions.

Example:

```
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: my-high-iops-class
provisioner: cinder.csi.openstack.org
parameters:
  type: high-iops
```

Apply with `kubectl apply -f storage-class.yaml`.

* `name`: Choose a unique one, as we don't want to interfere with the default names.
* `provisioner`: Use the one of your cluster. You can always have a look in the default class to verify the right provider.
* `type`: Use one of the [official provided types](/optimist/specs/volume_specification/#volume-type-list) from the Optimist platform (at the time of writing low-iops and high-iops).

To use the new storage class you need to change your volumes definitions and add the new StorageClass name.
