---
title: Einrichtung von StorageClasses
lang: "de"
permalink: /managedk8s/storageclasses/
nav_order: 4300
parent: FAQ
---
# Einrichtung von StorageClasses

Wir stellen pro Cluster eine Standard-StorageClass bereit.
> **Achtung:**
> Diese StorageClass wird von WIIT verwaltet und kann **jederzeit überschrieben werden**. Bitte erstellen Sie für Ihre Änderungen eine eigene StorageClass.

```
kubectl get storageclasses.storage.k8s.io
NAME                   PROVISIONER                AGE
cinder-csi (default)   cinder.csi.openstack.org   6h45m
```

## OpenStack-Volume-Typen

Die OpenStack-Volume-Typen, sortiert nach maximal möglichen IOPS:

* standard <- wird in der Standard-StorageClass verwendet
* high-iops

## Funktionen der Volumes

Wir stellen keine `Read-Write-Many`-Volumes bereit. Alle Volumes sind `Read-Write-Once`!

## Eigene StorageClasses hinzufügen

Wenn Sie einen anderen Typ verwenden müssen, können Sie eigene Definitionen hinzufügen.

Beispiel:

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

Wenden Sie die Definition mit `kubectl apply -f storage-class.yaml` an.

* `name`: Wählen Sie einen eindeutigen Namen, damit es keinen Konflikt mit der Standard-StorageClass gibt.
* `provisioner`: Verwenden Sie den Provisioner Ihres Clusters. Den richtigen Provisioner können Sie jederzeit in der Standard-StorageClass prüfen.
* `type`: Verwenden Sie einen der [offiziell bereitgestellten Typen](/openstack/specs/volume_specification/#volume-typen) der OpenStack-Plattform (zum Zeitpunkt der Erstellung dieses Textes standard und high-iops).
* `volumeBindingMode`: Es gibt zwei Optionen. Wir empfehlen `WaitForFirstConsumer`. Grund: Wenn Sie mehr als eine Availability Zone zusammen mit Richtlinien für die Pod-Platzierung verwenden, kann `Immediate` zu unvorhersehbaren Problemen beim Scheduling führen.
  * `WaitForFirstConsumer`: Das Volume wird erstellt, wenn ein Pod es anfordert.
  * `Immediate`: Das Volume wird in dem Moment erstellt, in dem der PVC erstellt wird.

Um die neue StorageClass zu verwenden, müssen Sie Ihre Volume-Definitionen ändern und den Namen der neuen StorageClass hinzufügen.
