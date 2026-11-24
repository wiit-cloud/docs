---
title: Load Balancer
lang: "de"
permalink: /managedk8s/clusterlifecycle/loadbalancer/
nav_order: 3345
parent: Cluster Lifecycle
---
# Load Balancer

Workload Cluster verwenden [OpenStack Octavia](/openstack/networking/octavia_loadbalancer/), um Kubernetes-Services vom Typ `type: LoadBalancer` umzusetzen. Zwei Octavia-Provider sind verfügbar:

- **`amphora`** (Standard) - Octavia startet für jeden Load Balancer eine eigene virtuelle Maschine (Amphora), auf der HAProxy läuft.
- **`ovn`** - OVN setzt das Load Balancing nativ um, ohne eigene virtuelle Maschinen.

## Provider auswählen {#choosing-a-provider}

Der Provider wird einmal pro Cluster bei der [Erstellung des Clusters](/managedk8s/clusterlifecycle/clustercreation/) festgelegt. Er gilt für jeden Service vom Typ `type: LoadBalancer`, der in diesem Cluster erstellt wird. Um den Provider `ovn` anzufragen, fügen Sie Ihrer Anfrage zur Erstellung des Clusters Folgendes hinzu:

```yaml
# by default: amphora
load_balancer_provider: ovn
```

Wenn Sie keinen bestimmten Grund für eine Änderung haben, behalten Sie den Standard (`amphora`).

## Unterschiede zu `amphora` {#differences-from-amphora}

- Es werden keine eigenen virtuellen Amphora-Maschinen erstellt. Load Balancer werden darum schneller bereitgestellt und verbrauchen weniger OpenStack-Ressourcen.
- Nur Layer 4 (TCP/UDP) wird unterstützt - Layer-7-Funktionen wie Routing auf Basis von Host oder Pfad, TLS-Terminierung oder Änderung von Headern sind nicht verfügbar.
- Der Prometheus-Metrik-Endpunkt (das Listener-Protokoll `PROMETHEUS`) wird nicht unterstützt.
- Die [Allowlist für den API-Server](/managedk8s/clusterlifecycle/apiallowlist/) wird nicht unterstützt. Anfragen, die beides kombinieren, werden abgelehnt.

Eine allgemeine Einführung in Octavia und die `openstack` CLI finden Sie unter [Octavia Loadbalancers](/openstack/networking/octavia_loadbalancer/#verwendung-des-ovn-loadbalancer-drivers).

## Erhalt der Quell-IP {#source-ip-preservation}

`amphora` leitet den Traffic mit Source NAT über die Amphora-Instanz. `ovn` macht das nicht: Es leitet den Traffic an Ihre Pods weiter und erhält dabei die ursprüngliche IP-Adresse des Clients von Ende zu Ende. Unsere Plattform verwaltet die dafür nötigen Security-Group-Regeln automatisch. Services vom Typ `type: LoadBalancer` funktionieren darum ohne manuelle Konfiguration auf Ihrer Seite.

## Load-Balancer-Optionen {#load-balancer-options}

Der [OpenStack Cloud Controller Manager](https://github.com/kubernetes/cloud-provider-openstack/blob/master/docs/openstack-cloud-controller-manager/using-openstack-cloud-controller-manager.md#load-balancer) erstellt und verwaltet die Octavia Load Balancer für Ihre Services vom Typ `type: LoadBalancer`. Eine Reihe von clusterweiten Optionen steuert sein Verhalten. WIIT verwaltet den Cloud Controller Manager. Sie können diese Optionen darum nicht innerhalb Ihres Clusters ändern.

### Optionen anfragen {#requesting-options}

Um eine oder mehrere Optionen zu aktivieren oder zu ändern, öffnen Sie ein Ticket über einen unserer [Support-Kanäle](/managedk8s/about/support/). Fügen Sie die folgende Spezifikation hinzu. Geben Sie nur die Optionen an, die Sie ändern möchten. Jede Option, die Sie weglassen, behält ihren Standardwert.

```yaml
load_balancer_options:
  # by default: ROUND_ROBIN
  lb-method: LEAST_CONNECTIONS
  # by default: true
  create-monitor: true
  # by default: 10s
  monitor-delay: 10s
  # by default: 3s
  monitor-timeout: 3s
  # by default: 3
  monitor-max-retries: 3
  # by default: 3
  monitor-max-retries-down: 3
  # by default: false
  internal-lb: false
  # by default: false
  enable-ingress-hostname: false
  # by default: 10
  max-shared-lb: 10
```

Sie können die Optionen bei der [Erstellung des Clusters](/managedk8s/clusterlifecycle/clustercreation/) anfragen oder für einen bestehenden Cluster als [Cluster-Änderung](/managedk8s/clusterlifecycle/clusterchanges/). Wir wenden die angefragten Optionen an oder kontaktieren Sie, wenn wir weitere Details benötigen.

### Annotationen pro Service {#per-service-annotations}

Wenn eine Einstellung nur für einige Services gelten soll, benötigen Sie kein Ticket. Die meisten Optionen können Sie mit einer Annotation an einem einzelnen Service setzen. Diese Annotation verwalten Sie selbst. Eine Annotation hat Vorrang vor der clusterweiten Option. Die Ausnahme ist `internal-lb: true` (siehe [`internal-lb`](#internal-lb)).

| Clusterweite Option | Service-Annotation |
| --- | --- |
| `lb-method` | `loadbalancer.openstack.org/lb-method` |
| `create-monitor` | `loadbalancer.openstack.org/enable-health-monitor` |
| `monitor-delay` | `loadbalancer.openstack.org/health-monitor-delay` |
| `monitor-timeout` | `loadbalancer.openstack.org/health-monitor-timeout` |
| `monitor-max-retries` | `loadbalancer.openstack.org/health-monitor-max-retries` |
| `monitor-max-retries-down` | `loadbalancer.openstack.org/health-monitor-max-retries-down` |
| `internal-lb` | `service.beta.kubernetes.io/openstack-internal-load-balancer` |
| `enable-ingress-hostname` | — (nur clusterweit) |
| `max-shared-lb` | — (nur clusterweit) |

Ein Beispiel: So verwenden Sie `LEAST_CONNECTIONS` mit einem schnelleren Health Check für einen Service:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-app
  annotations:
    loadbalancer.openstack.org/lb-method: LEAST_CONNECTIONS
    # in seconds, without unit
    loadbalancer.openstack.org/health-monitor-delay: "5"
spec:
  type: LoadBalancer
  selector:
    app: my-app
  ports:
    - port: 443
      targetPort: 8443
```

Es gibt weitere Annotationen ohne clusterweite Option, zum Beispiel um die Floating IP beim Löschen des Service zu behalten (`loadbalancer.openstack.org/keep-floatingip`), um das PROXY-Protokoll zu aktivieren (`loadbalancer.openstack.org/proxy-protocol`) oder um Timeouts für Listener zu setzen (`loadbalancer.openstack.org/timeout-*`).

Die vollständige Liste und die zulässigen Werte jeder Annotation finden Sie in der offiziellen OCCM-Dokumentation: [Service annotations](https://github.com/kubernetes/cloud-provider-openstack/blob/master/docs/openstack-cloud-controller-manager/expose-applications-using-loadbalancer-type-service.md#service-annotations).

### Referenz der Optionen {#option-reference}

#### `lb-method` {#lb-method}

Der Algorithmus, mit dem Verbindungen auf Ihre Nodes verteilt werden.

- `ROUND_ROBIN` (Standard) - Verbindungen werden gleichmäßig der Reihe nach verteilt.
- `LEAST_CONNECTIONS` - Jede neue Verbindung geht an das Backend mit den wenigsten aktiven Verbindungen.
- `SOURCE_IP` - Verbindungen von derselben Client-IP-Adresse gehen immer an dasselbe Backend.
- `SOURCE_IP_PORT` - Verbindungen von derselben Client-IP-Adresse und demselben Port gehen immer an dasselbe Backend.

Der Provider `ovn` unterstützt nur `SOURCE_IP_PORT`.

#### `create-monitor` {#create-monitor}

Erstellt für jeden Load-Balancer-Pool einen Octavia Health Monitor (`true` oder `false`, Standard `true`). Mit einem Health Monitor sendet Octavia keinen Traffic mehr an Nodes, die nicht antworten. Sobald diese Nodes wieder fehlerfrei sind, sendet Octavia wieder Traffic an sie. Ohne Health Monitor kann Traffic an einen nicht erreichbaren Node gehen, bis Kubernetes ihn aus dem Pool entfernt.

Lassen Sie den Health Monitor aktiviert, wenn Ihre Services `externalTrafficPolicy: Local` verwenden. Dann können nur Nodes antworten, auf denen ein Pod des Service läuft.

#### `monitor-delay` {#monitor-delay}

Der Abstand zwischen zwei Health Checks, als Dauer mit Einheit, zum Beispiel `10s` (Standard `10s`). Wird nur verwendet, wenn `create-monitor` den Wert `true` hat.

#### `monitor-timeout` {#monitor-timeout}

Wie lange ein Health Check auf eine Antwort wartet, bevor er als fehlgeschlagen gilt, als Dauer mit Einheit, zum Beispiel `3s` (Standard `3s`). Muss kleiner als `monitor-delay` sein. Wird nur verwendet, wenn `create-monitor` den Wert `true` hat.

#### `monitor-max-retries` {#monitor-max-retries}

Die Anzahl erfolgreicher Prüfungen in Folge, nach der ein Node wieder als fehlerfrei markiert wird, zwischen 1 und 10 (Standard `3`). Wird nur verwendet, wenn `create-monitor` den Wert `true` hat.

#### `monitor-max-retries-down` {#monitor-max-retries-down}

Die Anzahl fehlgeschlagener Prüfungen in Folge, nach der ein Node als fehlerhaft markiert wird und keinen Traffic mehr erhält, zwischen 1 und 10 (Standard `3`). Wird nur verwendet, wenn `create-monitor` den Wert `true` hat.

#### `internal-lb` {#internal-lb}

Bei `true` erhalten Load Balancer nur eine private IP-Adresse in Ihrem Cluster-Netzwerk und keine Floating IP. Sie sind darum nicht aus dem Internet erreichbar (Standard `false`). Verwenden Sie diese Option, wenn Ihre Services nur innerhalb Ihres OpenStack-Projekts oder über ein VPN erreichbar sein sollen.

Mit `internal-lb: true` ist jeder Load Balancer im Cluster intern. Die Annotation `service.beta.kubernetes.io/openstack-internal-load-balancer` wird dann ignoriert. Mit dem Standardwert `false` können Sie trotzdem einen einzelnen Service intern machen. Setzen Sie dazu diese Annotation auf `"true"`.

#### `max-shared-lb` {#max-shared-lb}

Die maximale Anzahl von Services, die sich über die Annotation `loadbalancer.openstack.org/load-balancer-id` einen Octavia Load Balancer teilen können, zwischen 1 und 999 (Standard `10`). Wenn Services Load Balancer teilen, benötigen Sie weniger Load Balancer und Floating IPs.

### Wichtige Hinweise {#things-to-keep-in-mind}

- Die Optionen gelten für alle Services vom Typ `type: LoadBalancer` im Cluster.
- Optionen, die oben nicht aufgeführt sind, zum Beispiel die Netzwerke und Subnetze für Load Balancer, verwaltet WIIT. Sie können sie nicht ändern.
