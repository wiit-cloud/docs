---
title: Root Disks
lang: "de"
permalink: /managedk8s/clusterlifecycle/rootdisk/
nav_order: 3355
parent: Cluster Lifecycle
---
# Root Disks

Sie können die Größe und den Typ der Disk für die Control Plane und für die Worker-Gruppen definieren. Bitte geben Sie diese Informationen im folgenden Format an:

```
# Control plane
disk_size_cp: <INT>
volume_type_cp: <vType>

# Worker
# default
disk_size: <INT>
volume_type: <vType>
```

Ersetzen Sie `<INT>` durch eine positive ganze Zahl. Diese Zahl ist die Größe der Root Disk in Gigabyte.

`<vType>` hängt von der verwendeten Plattform ab. Diese Information finden Sie in der Dokumentation der Plattform.
- [OpenStack](/openstack/specs/volume_specification/)

Der Standardwert ist `default`. Ändern Sie diesen Wert nur, wenn Sie wissen, dass Sie andere Anforderungen haben.

## Überlegungen
* Beachten Sie bei der Wahl der Disk-Größe: Heruntergeladene (pulled) Container-Images werden auf dieser Disk gespeichert. Wenn Sie viele große Images haben, passen Sie die Disk-Größe entsprechend an.
* Wenn Sie viele [ephemere Volumes](https://kubernetes.io/docs/concepts/storage/ephemeral-volumes/) verwenden oder große Datenmengen speichern oder verarbeiten, passen Sie die Disk-Größe entsprechend an.
* Wenn Sie eine hohe Rate an Änderungen über die Kubernetes-API erwarten, ziehen Sie eine Root Disk mit höheren IOPS für die Control Plane in Betracht.
* Wenn Sie eine Root Disk angeben, werden die zugehörigen Volumes in Ihrem Tenant erstellt.

**Hinweis:** Wenn nichts angegeben ist, ist der Standard-Volume-Typ `default` und die Größe 20 GB.
