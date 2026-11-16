---
title: Änderungen am Cluster
lang: "de"
permalink: /managedk8s/clusterlifecycle/clusterchanges/
nav_order: 3800
parent: Cluster Lifecycle
---

Wir unterstützen die folgenden Änderungen an einem laufenden CKS-Cluster:

**Upgrade der Kubernetes-Version**
- Führen Sie ein Upgrade Ihres Clusters auf eine neuere Kubernetes-Version durch.

**Rotation der kubeconfig**
- Rotieren Sie die kubeconfig aus Sicherheitsgründen oder aus betrieblichen Gründen.

**Änderungen an der Control Plane**

- Ändern Sie den Flavor der Control Plane.
- Aktivieren, ändern oder deaktivieren Sie die [API Server Allowlist](/managedk8s/clusterlifecycle/apiallowlist/).

**Änderungen an Machine Deployments**

- Fügen Sie neue Machine Deployments hinzu.
- Löschen Sie bestehende Machine Deployments.
- Ändern Sie bestehende Machine Deployments, unter anderem:
   - Name
   - Replicas
   - Flavor
   - Taints

**Bitte beachten Sie:** Wir können ein Volume nicht von einer Availability Zone (AZ) in eine andere migrieren. Wenn Sie die AZ eines Machine Deployments ändern wollen, beachten Sie: Dies kann Probleme mit den Volumes verursachen, die zu diesem Machine Deployment gehören.

**Änderungen an eigenen Root Volumes**
- Fügen Sie eigene Root Volumes für die Control Plane oder die Worker Nodes hinzu oder entfernen Sie diese, unter anderem:
   - Größe der Disk
   - Volume-Typ

**Funktion Cluster Autoscaler**
- Aktivieren oder deaktivieren Sie den cluster-autoscaler für ein bestimmtes Machine Deployment.
- Passen Sie die minimale und die maximale Anzahl der Worker Nodes für das Autoscaling an.

**Load-Balancer-Optionen**
- Aktivieren oder ändern Sie clusterweite [Load-Balancer-Optionen](/managedk8s/clusterlifecycle/loadbalancer/#load-balancer-options), zum Beispiel den Balancing-Algorithmus oder die Health Monitors.

**Component Extra Args**
- Übergeben Sie zusätzliche Kommandozeilen-Flags an Komponenten der Control Plane, an das kubelet der Control Plane oder an den cluster-autoscaler. Siehe [Component Extra Args](/managedk8s/clusterlifecycle/extraargs/).

## So fragen Sie Support für Ihren CKS-Cluster an
Unsere Support-Kanäle finden Sie [hier](/managedk8s/about/support/).

**Wenn Sie Änderungen an einem Machine Deployment anfragen, geben Sie bitte das Machine Deployment an, für das die Änderungen gelten sollen. Wir benötigen einen Namen im Format `md-name-az-md`**
**oder `cluster-name-az-md`, damit wir das richtige Machine Deployment identifizieren können.**

Wir führen die angefragten Änderungen durch. Wenn wir weitere Details benötigen, kontaktieren wir Sie, bevor wir die Änderungen auf Ihren Cluster anwenden.
