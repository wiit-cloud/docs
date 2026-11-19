---
title: Verschlüsselte Volumes
lang: "de"
permalink: /openstack/keymanager/encrypted_volumes/
parent: Key Manager
nav_order: 4002
last_modified_date: 2026-11-19
---

# Verschlüsselte Volumes

Verschlüsselte Volume-Typen verschlüsseln Daten transparent auf dem Compute-Host (LUKS, AES-XTS mit 256-Bit-Schlüsseln). Der Schlüssel wird beim Anlegen des Volumes automatisch erzeugt und im Key Manager hinterlegt — ein eigenes Key-Management ist nicht erforderlich. Innerhalb der Instanz erscheint das Volume als normales, unverschlüsseltes Blockdevice.

## Verfügbare Typen

| Name | QoS-Profil | Verschlüsselung |
| :--- | :--------- | :-------------- |
| `standard-enc` | standard | LUKS / AES-XTS / 256-bit |
| `high-iops-enc` | high-iops | LUKS / AES-XTS / 256-bit |

Die Typen spiegeln die Performance-Profile ihrer unverschlüsselten Gegenstücke — die QoS-Limits stehen in den [Volume Type Specifications](/openstack/specs/volume_specification/). Für den allgemeinen Umgang mit Secrets siehe [Secrets hinterlegen](/openstack/keymanager/secrets/).

## Verschlüsseltes Volume erstellen

```bash
(openstack) $ openstack volume create --type standard-enc --size 50 encrypted-data
(openstack) $ openstack server add volume my-server encrypted-data
```

Innerhalb der Instanz partitionieren und formatieren Sie das Device wie gewohnt — Ver- und Entschlüsselung laufen transparent auf dem Hypervisor.

## Verschlüsseltes Boot-Volume

Ein direkter Boot aus einem Image erzeugt kein verschlüsseltes Volume. Erstellen Sie zuerst ein bootfähiges Volume eines verschlüsselten Typs:

```bash
(openstack) $ openstack volume create --type standard-enc --size 20 \
    --image <image> --bootable encrypted-boot
(openstack) $ openstack server create --volume encrypted-boot \
    --flavor <flavor> --network <network> my-server
```

## Wissenswertes

* **Schlüssel-Lebenszyklus** — jedes verschlüsselte Volume erhält einen eigenen Schlüssel im Key Manager. Beim Löschen des Volumes wird der Schlüssel mitgelöscht. Löschen Sie den Schlüssel manuell im Key Manager, sind die Volume-Daten nicht wiederherstellbar — das lässt sich auch bewusst als Crypto-Erasure nutzen.
* **Snapshots und Backups** — Snapshots verschlüsselter Volumes bleiben verschlüsselt und referenzieren denselben Schlüssel. Backups werden für den Transport neu verschlüsselt und stellen neue verschlüsselte Volumes wieder her.
* **Retype** — ein Wechsel zwischen verschlüsselten und unverschlüsselten Typen wird nicht unterstützt. Migrieren Sie Daten stattdessen über ein zweites Volume.
* **Attached Volumes** — Detach und Attach benötigen Zugriff auf den Schlüssel. Vermeiden Sie Key-Manager-Operationen (Schlüssel löschen, ACLs ändern) auf Schlüsseln von eingebundenen Volumes.
