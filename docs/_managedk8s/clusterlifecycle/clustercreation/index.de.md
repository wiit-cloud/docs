---
title: Cluster-Erstellung
lang: "de"
permalink: /managedk8s/clusterlifecycle/clustercreation/
nav_order: 3100
parent: Cluster Lifecycle
---
# Cluster-Erstellung
Für eine erfolgreiche Cluster-Erstellung geben Sie bitte die unten beschriebenen erforderlichen Informationen an. Prüfen Sie vor der Cluster-Erstellung, dass alle Angaben vollständig und korrekt sind.
Kopieren Sie den folgenden Block und ergänzen Sie die nötigen Informationen für Ihren neuen Cluster:
```
# required information
cluster_name: 
customer_id: 

# Kubernetes version
# by default the latest supported k8s minor version provided by us
kubernetes_version: 

# control plane flavor
# by default s1.medium
flavor: 

## Machine deployments: one block per MD (default: 1 md, 3 replicas, s1.large, random AZ)
# machine deployment name
# by default clusterName-az-md
md_name:  
# replicas by default 3
replicas:
# by default s1.large
flavor: 
# by default random-az
availability_zone: 
# supported values: az1, az2, az3, sz1 (sz1 may not be combined with other AZs)
# warning: sz1 is a special stretched zone, see https://docs.wiit-cloud.io/openstack/intro/#stretched-zone-sz1

## add the autoscaler (default: disabled)
min_size:
max_size:

## restrict Kubernetes API access to these CIDRs (default: open to everyone)
api_server_allowed_cidrs:
```
Detaillierte Informationen zu den Optionen finden Sie weiter unten.

## Optionale Einstellungen

Sie können die IP-Bereiche für Pod-Adressen und Service-Adressen anpassen.
Wenn Sie keine Werte angeben, verwenden wir die unten gezeigten Standardwerte.
Ändern Sie diese Werte nur, wenn sie mit einem extern gerouteten Netzwerk kollidieren.

```
# the IP range for pod allocation
pod_cidr: 172.25.0.0/16

# the IP range for service allocation
service_cidr: 10.96.0.0/12

# the CoreDNS address is the 10th address of the service CIDR
dns_service_address: 10.96.0.10
```

## Was Sie erhalten
Nach der Cluster-Erstellung erhalten Sie über einen sicheren Weg eine Admin-kubeconfig. Zusätzlich erhalten Sie einen [WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name) für spätere Support-Anfragen.

**Für die weitere Kommunikation benötigen wir den [WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name), um Ihren Cluster zu identifizieren.**

# Detaillierte Informationen

## Voraussetzungen
### OpenStack-Zugangsdaten
Sie benötigen einen bestehenden oder neu erstellten OpenStack-Tenant. Erstellen Sie in diesem Tenant Folgendes und stellen Sie es uns bereit:
- [Application Credentials](/managedk8s/clusterlifecycle/appcredentials/), die mit dem Projekt verknüpft sind, in dem wir den neuen Cluster erstellen sollen.


### Erforderliche Informationen für den neuen Cluster
* Cluster-Name: Maximal 22 Zeichen.
* Customer ID: Ohne Angabe verwenden wir die Firmen-ID-Nummer.
* Application Credentials: Wie in den Voraussetzungen beschrieben.

### Optionale Anforderungen und konfigurierbare Funktionen (mit Standardwerten)
#### Kubernetes-Version
Ohne Angabe stellen wir den Cluster mit der neuesten unterstützten Kubernetes-Minor-Version bereit.
Sie können die Version major.minor manuell festlegen (z. B. 1.33). Dann müssen Sie die Version manuell aktualisieren, wenn eine neue Version nötig ist.
Das Patch-Level aktualisieren wir, und wir stellen es automatisch bereit.

Weitere Details zu unterstützten Versionen, Abkündigungen, EOL oder anderen Fragen zu Versionen finden Sie [hier](/managedk8s/about/kubernetesversions/).

#### OpenStack-Netzwerk
Geben Sie eine bestehende Netzwerk-ID an. Andernfalls erstellen wir ein neues Netzwerk für Sie.
Bereitgestellte Netzwerke löschen wir nicht, wenn wir den Cluster löschen. Sie können diese Netzwerke nach der Cluster-Löschung wiederverwenden.
Automatisch erstellte Netzwerke löschen wir zusammen mit dem Cluster.

Wenn Sie keine besonderen Anforderungen haben, verwenden Sie ein automatisch erstelltes Netzwerk. Dann ist auf unserer Seite kein separater Prozess für Erstellung oder Bereinigung nötig.
Für Anforderungen wie gemeinsam genutzte Netzwerke, zusätzliche VPNs usw. verwenden Sie bitte die bereitgestellten Optionen.

#### Load-Balancer-Provider
OpenStack Octavia stellt die Services vom `type: LoadBalancer` bereit. Standardmäßig verwenden wir den Provider `amphora`. Der Provider `ovn` ist ebenfalls verfügbar.

Details finden Sie [hier](/managedk8s/clusterlifecycle/loadbalancer/).

#### API-Server-Allowlist
Standardmäßig ist die Kubernetes API von überall erreichbar. Sie können den Zugriff auf eine Liste von IP-Bereichen beschränken.
Diese Funktion ist mit dem Load-Balancer-Provider `ovn` nicht verfügbar.

Details finden Sie [hier](/managedk8s/clusterlifecycle/apiallowlist/).

### Machine Deployments, Worker Nodes und Autoscaling
Für Machine Deployments lesen Sie bitte die ausführlichere [Dokumentation](/managedk8s/clusterlifecycle/machinedeployments/). Dort gibt es weitere Optionen.

Standardmäßig erhalten Sie ein einzelnes Machine Deployment mit 3 Nodes in einer AZ.

#### Cluster Autoscaler (gleich für jedes Machine Deployment, wenn es mehrere gibt)
Wir unterstützen den Cluster Autoscaler. Wir können ihn für jedes Machine Deployment separat aktivieren.

Details finden Sie [hier](/managedk8s/clusterlifecycle/autoscaling/).
