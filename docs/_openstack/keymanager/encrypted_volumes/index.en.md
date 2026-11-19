---
title: Encrypted Volumes
lang: "en"
permalink: /openstack/keymanager/encrypted_volumes/
parent: Key Manager
nav_order: 4002
last_modified_date: 2026-11-19
---

# Encrypted Volumes

Encrypted volume types encrypt data transparently on the compute host (LUKS, AES-XTS with 256-bit keys). The encryption key is generated automatically when the volume is created and stored in the key manager — no key management on your side is required. Inside the instance the volume appears as a normal, unencrypted block device.

## Available types

| Name | QoS profile | Encryption |
| :--- | :---------- | :--------- |
| `standard-enc` | standard | LUKS / AES-XTS / 256-bit |
| `high-iops-enc` | high-iops | LUKS / AES-XTS / 256-bit |

The types mirror the performance profiles of their unencrypted counterparts — see [Volume Type Specifications](/openstack/specs/volume_specification/) for the QoS limits. For general secret handling see [Storing Secrets](/openstack/keymanager/secrets/).

## Create an encrypted volume

```bash
(openstack) $ openstack volume create --type standard-enc --size 50 encrypted-data
(openstack) $ openstack server add volume my-server encrypted-data
```

Inside the instance, partition and format the device as usual — encryption and decryption happen transparently on the hypervisor.

## Encrypted boot volume

Booting directly from an image does not encrypt the volume. Create a bootable volume of an encrypted type first:

```bash
(openstack) $ openstack volume create --type standard-enc --size 20 \
    --image <image> --bootable encrypted-boot
(openstack) $ openstack server create --volume encrypted-boot \
    --flavor <flavor> --network <network> my-server
```

## Things to know

* **Key lifecycle** — every encrypted volume gets its own key in the key manager. Deleting the volume deletes the key. If you delete the key in the key manager manually, the volume data is unrecoverable — this can also be used deliberately for crypto-erasure.
* **Snapshots and backups** — snapshots of encrypted volumes stay encrypted and reference the same key. Backups are re-encrypted for transport and restore into new encrypted volumes.
* **Retype** — changing a volume between encrypted and unencrypted types is not supported. Migrate data via a second volume instead.
* **Attached volumes** — detach and attach require access to the encryption key. Avoid key manager operations (key deletion, ACL changes) on keys of attached volumes.
