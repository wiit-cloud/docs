---
title: Cluster Autoscaler
lang: "de"
permalink: /managedk8s/clusterlifecycle/autoscaling/
nav_order: 3510
parent: Cluster Lifecycle
---
# Cluster Node Autoscaler
Der Cluster Node Autoscaler passt die Anzahl der Nodes in einem Kubernetes-Cluster automatisch an den aktuellen Workload an. Er sorgt für eine effiziente Ressourcennutzung: Er vergrößert den Cluster, wenn die Kapazität für alle eingeplanten Pods nicht ausreicht. Er verkleinert den Cluster, wenn es ungenutzte Nodes gibt.

# Wie der Cluster Autoscaler funktioniert
**Hochskalieren:**
Wenn der Autoscaler erkennt, dass einige Pods wegen zu wenig Ressourcen (CPU, Arbeitsspeicher) nicht eingeplant werden können, fügt er dem Cluster automatisch neue Nodes hinzu. So stellt er die nötige Kapazität bereit. Er bewertet die Kapazität, die für Kubernetes-Pods verfügbar ist, nicht die reine CPU und den reinen Arbeitsspeicher des OpenStack-Flavors. Node-Reservierungen und Requests von Plattformkomponenten verringern die Kapazität, die für Ihre Workloads verfügbar ist. Details finden Sie unter [Allocatable Resources](/managedk8s/clusterlifecycle/allocatable-resources/).

**Herunterskalieren:**
Wenn der Autoscaler Nodes findet, die über einen konfigurierbaren Zeitraum wenig ausgelastet oder ganz ungenutzt sind, entfernt er diese Nodes. So optimiert er die Ressourcennutzung und senkt die Kosten. Vor dem Herunterskalieren stellt er sicher, dass auf diesen Nodes keine kritischen Pods laufen und dass die Workloads sicher auf andere Nodes verschoben werden können.

Der Cluster Node Autoscaler ist standardmäßig nicht aktiviert. Um ihn zu aktivieren, kontaktieren Sie uns bitte. Folgen Sie dazu den Anweisungen [hier](/managedk8s/about/support/).

Geben Sie in Ihrer Anfrage die folgenden Informationen an:

- [WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name)
- Den Namen oder die Namen des betroffenen Machine Deployments oder der betroffenen Machine Deployments
- Minimale Anzahl der Worker Nodes im betroffenen Machine Deployment: Legen Sie die minimale Anzahl der Nodes fest, die im betroffenen Machine Deployment immer vorhanden sein sollen.
- Maximale Anzahl der Worker Nodes: Legen Sie die maximale Anzahl der Nodes im betroffenen Machine Deployment fest, bis zu der der Cluster hochskalieren kann.

Sie können die Cluster-Autoscaler-Funktion jederzeit für ein oder mehrere Machine Deployments aktivieren oder deaktivieren.

**Hinweis:** Autoscaling-Entscheidungen basieren auf den Resource Requests der Pods. Auch die Plattformkomponenten in Ihrem Cluster deklarieren Resource Requests.

# Zwei Konfigurationsebenen

Der Autoscaler hat zwei Geltungsbereiche. Sie können einen davon oder beide verwenden.

## Pro Worker-Gruppe

Die Aktivierung des Autoscalers und die minimale und maximale Anzahl der Nodes gelten pro Worker-Gruppe.
Sie können auch das Verhalten beim Herunterskalieren und bei der Bereitstellung für eine einzelne Worker-Gruppe anpassen. Jede
Option überschreibt den clusterweiten Standardwert nur für diese Gruppe:

| Option | Steuert |
|---|---|
| `scaledownutilizationthreshold` | Auslastung, unter der ein Node für die Entfernung in Frage kommt (z. B. `0.5`). |
| `scaledowngpuutilizationthreshold` | Derselbe Schwellenwert für GPU-Nodes. |
| `scaledownunneededtime` | Wie lange ein Node nicht benötigt sein muss, bevor er entfernt wird (z. B. `10m0s`). |
| `scaledownunreadytime` | Wie lange ein nicht bereiter Node wartet, bevor er entfernt wird. |
| `maxnodeprovisiontime` | Wie lange der Autoscaler wartet, bis sich ein neuer Node registriert. |
| `maxnodestartuptime` | Wie lange der Autoscaler wartet, bis ein Node bereit ist. |

## Clusterweit

Der Autoscaler läuft als ein einzelner Prozess für den gesamten Cluster. Flags auf Prozessebene gelten
gleichzeitig für alle Worker-Gruppen. Sie setzen diese Flags über Extra Args (siehe
[Component Extra Args](/managedk8s/clusterlifecycle/extraargs/)).

Um einen der beiden Geltungsbereiche anzufragen, stellen Sie eine [Support-Anfrage](/managedk8s/about/support/). Geben Sie Ihren
[WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name), bei Änderungen pro Gruppe den Namen der Worker-Gruppe
und die gewünschten Optionen oder Flags an.

## Skalierung von 0

Wir unterstützen die Skalierung von null. Sie können die minimale Anzahl der Nodes auf 0 setzen. Wenn es keine Pods gibt, skaliert der Autoscaler dann auf 0 Worker Nodes herunter. Wenn ein wartender Workload einen neuen Node benötigt, verwendet der Autoscaler die berechnete zuweisbare CPU und den berechneten zuweisbaren Arbeitsspeicher des gewählten Worker-Flavors. Er nimmt nicht an, dass die volle Kapazität des Flavors für Pods verfügbar ist. Plattformkomponenten werden nach der Erstellung des Nodes eingeplant und verbrauchen ihre Resource Requests.

Eine ausführliche Erklärung zu Reservierungen, zuweisbarer Kapazität und Requests von Plattform-Pods finden Sie unter [Allocatable Resources](/managedk8s/clusterlifecycle/allocatable-resources/).
