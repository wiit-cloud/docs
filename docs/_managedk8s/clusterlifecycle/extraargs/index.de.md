---
title: Component Extra Args
lang: "de"
permalink: /managedk8s/clusterlifecycle/extraargs/
nav_order: 3360
parent: Cluster Lifecycle
---
# Component Extra Args

Sie können zusätzliche Kommandozeilen-Flags an mehrere Komponenten des Clusters übergeben. Damit aktivieren Sie
Verhalten, für das es keine eigene Einstellung gibt, zum Beispiel zusätzliches Audit-Logging auf dem API-Server
oder Issuer-Flags für Workload Identity.

Stellen Sie eine [Support-Anfrage](/managedk8s/about/support/). Geben Sie darin die Komponente, den Namen des Flags
und den Wert des Flags an. Wir fügen die Flags Ihrem Cluster hinzu.

## Komponenten, die Extra Args akzeptieren

| Komponente                      | Was sie steuert                                                                                                                                                                                                                          |
|---------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `kube-apiserver`                | Der API-Server des Clusters. Hier wird auch der OIDC-Login konfiguriert (siehe [OIDC](/managedk8s/clusterlifecycle/oidc/)).                                                                                                              |
| `kube-controller-manager`       | Zentrale Steuerschleifen (z. B. Garbage Collection).                                                                                                                                                                                     |
| `kube-scheduler`                | Scheduling von Pods.                                                                                                                                                                                                                     |
| `etcd`                          | Der Datenspeicher des Clusters (falsche Flags können hier Daten beschädigen, fragen Sie diese Flags mit Vorsicht an).                                                                                                                    |
| `kubelet` (Control-Plane-Nodes) | Nur das kubelet auf den Control-Plane-Nodes. Flags für das kubelet auf Worker Nodes sind nicht konfigurierbar.                                                                                                                           |
| `cluster-autoscaler`            | Flags für den einzelnen, clusterweiten Autoscaler-Prozess. Diese Flags gelten für jede Worker-Gruppe. Einstellungen pro Worker-Gruppe (Aktivierung, Minimum, Maximum, Abstimmung des Herunterskalierens) finden Sie unter [Autoscaling](/managedk8s/clusterlifecycle/autoscaling/). |

Ein Wert, den Sie setzen, überschreibt unseren Standardwert für dieses Flag.

## Reservierte Flags

Einige Flags verwalten wir selbst. Sie können diese Flags nicht setzen. Wir lehnen eine Anfrage ab, die eines dieser Flags enthält.

| Komponente                | Reservierte Flags                                                                                    |
|---------------------------|------------------------------------------------------------------------------------------------------|
| `kube-apiserver`          | keine                                                                                                |
| `kube-controller-manager` | `flex-volume-plugin-dir`                                                                             |
| `kube-scheduler`          | keine                                                                                                |
| `etcd`                    | keine                                                                                                |
| `kubelet`                 | `cloud-provider`                                                                                     |
| `cluster-autoscaler`      | `cloud-provider`, `kubeconfig`, `clusterapi-cloud-config-authoritative`, `node-group-auto-discovery` |

{: .warning }
Extra Args ändern das Verhalten von Komponenten auf niedriger Ebene. Ein falsches Flag kann Ihren Cluster beschädigen,
auch die Control Plane. Wir können das nicht immer automatisch wiederherstellen. Sie sind für die Flags verantwortlich,
die Sie anfragen. Wir können ein Flag ablehnen, das den Plattformbetrieb oder die Compliance gefährdet.

## So stellen Sie eine Anfrage

Stellen Sie eine [Support-Anfrage](/managedk8s/about/support/). Geben Sie darin Ihren
[WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name) und für jede Komponente die gewünschten
Flags und Werte an.

Wir wenden die Flags an und rollen die betroffenen Komponenten neu aus. Wenn ein Flag reserviert ist oder
riskant aussieht, kontaktieren wir Sie.
