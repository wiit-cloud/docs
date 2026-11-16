---
title: API Server Allowlist
lang: "de"
permalink: /managedk8s/clusterlifecycle/apiallowlist/
nav_order: 3347
parent: Cluster Lifecycle
---
# API Server Allowlist

Standardmäßig ist die Kubernetes-API Ihres Clusters von jeder IPv4-Adresse aus erreichbar. Nur die Authentifizierung schützt sie.
Mit der API Server Allowlist können Sie den Netzwerkzugriff auf die Kubernetes-API zusätzlich auf eine Liste vertrauenswürdiger IP-Bereiche (CIDRs) beschränken.

Die Allowlist betrifft nur die Kubernetes-API. Ihre Anwendungen und deren Services vom `type: LoadBalancer` bleiben wie bisher erreichbar.

{: .warning }
Sie können die API Server Allowlist nicht mit dem [Load-Balancer-Provider](/managedk8s/clusterlifecycle/loadbalancer/) `ovn` kombinieren. Wir lehnen Anfragen ab, die sowohl `api_server_allowed_cidrs` als auch `load_balancer_provider: ovn` setzen. Verwenden Sie den Standard-Provider (`amphora`), wenn Sie die Allowlist benötigen.

## Allowlist anfragen

Fügen Sie die erlaubten IP-Bereiche Ihrer Anfrage zur [Erstellung des Clusters](/managedk8s/clusterlifecycle/clustercreation/) hinzu. Für einen bestehenden Cluster fragen Sie die IP-Bereiche als [Änderung am Cluster](/managedk8s/clusterlifecycle/clusterchanges/) an:

```yaml
# by default: empty (API reachable from everywhere)
api_server_allowed_cidrs:
  - w.x.y.z/24   # office
  - a.b.c.d/32   # VPN gateway
```

- Nur IPv4-Bereiche werden unterstützt.
- Verwenden Sie die CIDR-Notation. Eine einzelne Adresse schreiben Sie als `/32`.
- Eine leere Liste deaktiviert die Allowlist wieder.

## Was wir von Ihnen benötigen

Geben Sie jeden öffentlichen IP-Bereich an, von dem aus auf die Kubernetes-API zugegriffen wird, zum Beispiel:

- Büronetzwerke und VPN-Gateways Ihrer Administratoren und Entwickler
- CI/CD-Runner, die in den Cluster deployen
- externe Werkzeuge, die mit der Kubernetes-API kommunizieren (GitOps, Backup, Monitoring, ...)

Verwenden Sie die öffentliche IP-Adresse, mit welcher der Datenverkehr Ihr Netzwerk verlässt (die NAT- oder Egress-Adresse). Verwenden Sie keine internen Adressen.

Workloads im Cluster, welche die Kubernetes-API verwenden (z. B. Operatoren oder Controller), benötigen keinen Eintrag.

## Von WIIT hinzugefügte Adressen

Zusätzlich zu Ihren Bereichen fügen wir automatisch einige Adressen hinzu:

- Adressen, die der Cluster selbst für seine Funktion benötigt (sein eigenes Netzwerk und sein Router)
- die WIIT-Adressen, mit denen wir Ihren Cluster betreiben, überwachen und unterstützen:

| Adresse            | Verwendet für                     |
| ------------------ | --------------------------------- |
| `62.141.47.6/32`   | WIIT-Betriebspersonal             |
| `89.163.172.24/32` | WIIT-Clusterverwaltung            |
| `89.163.172.39/32` | WIIT-Plattformdienste             |

Wir wenden die Allowlist auf den Listener des Load Balancers der Kubernetes-API in Ihrem OpenStack-Projekt an, nicht auf eine Security Group.
Diese Adressen sehen Sie neben Ihren eigenen Bereichen in den erlaubten CIDRs des Listeners:

```bash
# the listener name ends with "-kubeapi-6443"
openstack loadbalancer listener list -c id -c name
openstack loadbalancer listener show <listener-id> -c allowed_cidrs
```

**Bitte entfernen oder ändern Sie diese Adressen nicht.** Ohne diese Adressen können wir Ihren Cluster nicht verwalten, und Updates oder Support können fehlschlagen.
Änderungen, die Sie direkt am Listener machen, werden automatisch auf die angefragte Liste zurückgesetzt.
Wenn Sie Fragen zu einer Adresse haben, wenden Sie sich an den [Support](/managedk8s/about/support/).

## Wichtige Hinweise

- Wir verwerfen Anfragen von Adressen, die nicht auf der Allowlist stehen. `kubectl` gibt dann keinen Fehler zurück, sondern läuft einfach in ein Timeout.
- Halten Sie die Liste aktuell, wenn sich die Adressen Ihres Büros, Ihres VPN oder Ihrer CI/CD ändern. Sonst sperren Sie sich selbst aus. Wir können weiterhin auf den Cluster zugreifen und die Liste auf Ihre Anfrage aktualisieren.
