---
title: Zuweisbare Ressourcen
lang: "de"
permalink: /managedk8s/clusterlifecycle/allocatable-resources/
nav_order: 3530
parent: Cluster Lifecycle
---
# Zuweisbare Ressourcen

CPU und Arbeitsspeicher eines OpenStack-Flavors beschreiben die Kapazität der virtuellen Maschine. Sie zeigen nicht die Kapazität, die für Ihre Anwendungen verfügbar ist. Jeder Worker Node benötigt Kapazität für das Betriebssystem, für Kubernetes selbst und für die verwalteten Plattformkomponenten, die in Ihrem Cluster laufen.

## Von der Flavor-Kapazität zur Workload-Kapazität {#from-flavor-capacity-to-workload-capacity}

Für Worker Nodes teilt Kubernetes die Flavor-Kapazität wie folgt auf:

- **`systemReserved`** ist für das Betriebssystem und die Systemdienste reserviert.
- **`kubeReserved`** ist für die Kubernetes-Node-Komponenten und die Container-Runtime reserviert. Der Wert wird für jeden Flavor berechnet.
- **Node allocatable** ist die verbleibende Kapazität, auf die Kubernetes Pods einplanen kann. Die Berechnung des Arbeitsspeichers enthält zusätzlich eine kleine Eviction-Reserve. So kann der Node sicher auf Speicherdruck reagieren.

Die Reservierungen gelten auf dem Node, bevor Ihre Anwendungs-Pods eingeplant werden. Ihre Werte hängen vom gewählten Flavor ab. Sie sind kein fester Prozentsatz für jeden Node.

### Beispiele: ein kleiner, ein großer und ein sehr großer Worker Node {#examples-a-small-a-large-and-an-extra-large-worker-node}

Die aktuelle Reservierungsrichtlinie ergibt die folgenden Werte für drei Worker-Flavors:

| Kapazitätsebene | `s1.small` CPU | `s1.small` Arbeitsspeicher | `s1.large` CPU | `s1.large` Arbeitsspeicher | `s1.2xlarge` CPU | `s1.2xlarge` Arbeitsspeicher |
| --- | ---: | ---: | ---: | ---: | ---: | ---: |
| Kapazität des OpenStack-Flavors | 2.000m (2 vCPU) | 4.096Mi (4 GiB) | 8.000m (8 vCPU) | 16.384Mi (16 GiB) | 30.000m (30 vCPU) | 65.536Mi (64 GiB) |
| `systemReserved` | 80m | 256Mi | 80m | 256Mi | 80m | 256Mi |
| `kubeReserved` | 20m | 1.023Mi | 20m | 2.661Mi | 65m | 5.610Mi |
| Eviction-Reserve für Arbeitsspeicher | — | 100Mi | — | 100Mi | — | 100Mi |
| **Node allocatable** | **1.900m** | **2.717Mi** | **7.900m** | **13.367Mi** | **29.855m** | **59.570Mi** |

`s1.large` hat mehr vCPUs als `s1.small`. Trotzdem reservieren beide 100m CPU. Der Grund: Wir ziehen die feste Systembasis von 80m vom größenabhängigen Zielwert ab. Für jeden Flavor mit weniger als 12 vCPUs liegt das Ergebnis unter dem Minimum von 20m. Darum ist `kubeReserved` 20m. Bei 12 vCPUs erreicht das Ergebnis 20m. Danach addiert jede weitere vCPU 2,5m (`0.25%` von 1.000m). Darum verwendet `s1.2xlarge` mit 30 vCPUs `20m + 18 × 2.5m = 65m` für `kubeReserved` und insgesamt 145m. Der zuweisbare Arbeitsspeicher ist 2.717Mi für `s1.small`, 13.367Mi für `s1.large` und 59.570Mi für `s1.2xlarge`. Die Requests von Plattform- und Anwendungs-Pods sind darin noch nicht abgezogen.

Die absolute Reservierung kann mit der Größe des Nodes steigen. Ihr Anteil sinkt aber. Für `s1.small` belegen die System-, Kubernetes- und Eviction-Reservierungen 1.379Mi (33,7 %) des Arbeitsspeichers und 100m (5 %) der CPU. Für `s1.large` belegen sie 3.017Mi (18,4 %) des Arbeitsspeichers und 100m (1,25 %) der CPU. Die genauen Werte hängen vom Flavor ab. Sie können sich ändern, wenn wir die Reservierungsrichtlinie aktualisieren.

## Plattformkomponenten belegen auch Node-Kapazität {#platform-components-also-consume-node-capacity}

Ein verwalteter Kubernetes-Cluster benötigt Plattformkomponenten für Netzwerk, DNS, OpenStack-Integration, Speicher und Metriken. Standardmäßig laufen in Ihrem Cluster Komponenten wie Calico, CoreDNS, der OpenStack Cloud Controller Manager, Cinder CSI, `kube-proxy`, Metrics Server, NodeLocal DNS und ein täglicher Job zur etcd-Defragmentierung. WIIT verwaltet diese Komponenten. Sie sind kein Teil Ihrer Workloads. GPU Discovery ist optional und nur für GPU Worker Nodes relevant. Die genaue Menge an Komponenten kann sich je nach Cluster-Konfiguration unterscheiden.

Diese Plattformkomponenten definieren Resource Requests und, wo sinnvoll, Ressourcenlimits. Der Scheduler verwendet die Requests. Darum verringern Requests die Kapazität, die für Ihre Workloads verfügbar ist. Limits begrenzen den Verbrauch zur Laufzeit. Sie reservieren aber keine zusätzliche Kapazität für das Scheduling. Requests von DaemonSets, Deployments und Jobs gelten auf den Nodes, auf denen ihre Pods laufen. Ein DaemonSet platziert normalerweise einen Pod auf jedem geeigneten Node. Die Kapazität für Ihre Anwendungs-Pods ist darum die zuweisbare Kapazität des Nodes minus die Requests der Plattform-Pods auf diesem Node und minus die Requests Ihrer anderen Workloads.

Weitere Informationen finden Sie in der [Kubernetes-Dokumentation](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits).

## Auswirkungen auf Workloads und Autoscaling {#impact-on-workloads-and-autoscaling}

Planen Sie die Worker-Kapazität mit den zuweisbaren Ressourcen der Nodes, nicht mit der reinen Größe des OpenStack-Flavors. Definieren Sie realistische CPU- und Arbeitsspeicher-Requests für Ihre Anwendungs-Container. So können der Scheduler und der [Cluster Autoscaler](/managedk8s/clusterlifecycle/autoscaling/) Entscheidungen nach den Ressourcen treffen, die Ihre Workloads wirklich benötigen.

Der Cluster Autoscaler verwendet die berechnete zuweisbare Kapazität auch, wenn er eine Worker-Gruppe von null skaliert. Er nimmt nicht an, dass ein neuer Node die volle CPU und den vollen Arbeitsspeicher seines Flavors bietet. Weitere Informationen finden Sie unter [Cluster Autoscaler](/managedk8s/clusterlifecycle/autoscaling/).
