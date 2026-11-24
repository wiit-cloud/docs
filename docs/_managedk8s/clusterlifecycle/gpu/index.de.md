---
title: GPU Worker
lang: "de"
permalink: /managedk8s/clusterlifecycle/gpu/
nav_order: 3520
parent: Cluster Lifecycle
---
# GPU Worker Nodes

Unsere Kubernetes-Cluster unterstützen die [OpenStack NVIDIA GPU Flavors](https://docs.wiit-cloud.io/openstack/specs/flavor_specification/#g1-gpu-flavors---only-available-in-sz1).

Wählen Sie für Ihre Worker-Gruppe einen passenden Flavor mit GPU. Damit aktivieren Sie die GPU-Unterstützung.

Wir verwalten GPU Worker Nodes wie normale Worker Nodes. Darum gelten die meisten Hinweise zu Konfiguration und Betrieb für beide gleich.

## Extras {#extras}
### Node-Labels {#node-labels}
Wir verwenden den GPU Discovery Service. Er setzt auf jedem Node eine Reihe von Labels. So sind alle GPU-Informationen an einer Stelle.

### Wartung {#maintenance}
GPU Machine Deployments verwenden dasselbe Wartungsfenster wie normale Machine Deployments.
Zusätzlich hat OpenStack einen eigenen [Wartungsmechanismus](https://docs.wiit-cloud.io/openstack/intro/#maintenance).

Für eine einheitliche Sicht stellen wir die relevanten OpenStack-Metadaten als Kubernetes-Node-Labels bereit.

Wenn eine OpenStack-Wartung mit einem Neustart des Hypervisors geplant ist, setzen wir das Node-Label `openstack.cks.wiit-cloud.io/maintenance_planned_for` auf das geplante Datum des Neustarts.

Alle anderen Funktionen finden Sie unter [Machine Deployments](/managedk8s/clusterlifecycle/machinedeployments/).
