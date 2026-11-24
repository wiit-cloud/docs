---
title: FAQ
lang: "de"
permalink: /managedk8s/faq/
nav_order: 4000
has_children: true
---
# Häufig gestellte Fragen

## Welche Cluster-Größe empfehlen wir?
Für Hochverfügbarkeit und Fehlertoleranz empfehlen wir üblicherweise:
- 3 Control-Plane-Nodes mit dem Flavor s1.medium (4 vCPU / 8 GB RAM).
- 3 Worker Nodes: Mindestens zwei Worker Nodes pro Machine Deployment verteilen den Workload und geben Redundanz. Wenn ein Worker Node ausfällt, führen die anderen Worker Nodes die Anwendungen weiter aus. So bleibt die Ausfallzeit gering.

Für Machine Deployments empfehlen wir: Erstellen Sie in jeder Availability Zone (AZ) ein separates Machine Deployment. So bleibt Ihre Anwendung verfügbar, auch wenn eine oder mehrere AZs ausfallen.

Erstellen Sie zum Beispiel drei Machine Deployments:
 * Machine Deployment in az1
 * Machine Deployment in az2
 * Machine Deployment in az3

Konfigurieren Sie für jedes Machine Deployment mindestens 2 Replicas. So haben Sie Redundanz in jeder AZ.


## Was kann ich wo konfigurieren?
Im Allgemeinen können Sie:
* den Flavor pro Machine Deployment festlegen
* die Replicas pro Machine Deployment festlegen
* die AZs pro Machine Deployment festlegen

Im Allgemeinen ergibt sich die Anzahl Ihrer VMs aus Replicas, Machine Deployments und Availability Zones. Die Anzahl der Replicas kann pro Machine Deployment unterschiedlich sein. Zum Beispiel:
* Sie haben für Ihren normalen Workload ein Machine Deployment in allen 3 AZs mit 3 Replicas. Dann erhalten Sie: 1 (Machine Deployment) * 3 (AZs) * 3 (Replicas) = 9 Nodes.
* Sie haben für Ihre Infrastruktur-Tools ein Machine Deployment in allen 3 AZs mit 2 Replicas. Dann erhalten Sie: 1 (Machine Deployment) * 3 (AZs) * 2 (Replicas) = 6 Nodes.

Die Anzahl der VMs ist also das Ergebnis einer Multiplikation. Wenn Sie mehr als eine AZ verwenden (das empfehlen wir dringend), können Sie darum zum Beispiel nicht 5 VMs haben.


## Was ist der Cluster Autoscaler?
Der Cluster Autoscaler ist ein eigenständiges Programm. Es passt die Größe eines Kubernetes-Clusters an den aktuellen Bedarf an.

## Wann ändert der Cluster Autoscaler die Größe eines Clusters?
Der Cluster Autoscaler vergrößert den Cluster, wenn:
  - Pods wegen zu wenig Ressourcen auf keinem der aktuellen Nodes eingeplant werden konnten.
  - ein zusätzlicher Node, ähnlich den aktuellen Nodes im Cluster, helfen würde.

Der Cluster Autoscaler verkleinert den Cluster, wenn einige Nodes über einen längeren Zeitraum dauerhaft nicht benötigt werden. Ein Node wird nicht benötigt, wenn seine Auslastung niedrig ist und alle seine wichtigen Pods auf andere Nodes verschoben werden können.

## Können wir eine bestimmte IP für einen Kubernetes Service vom Typ LoadBalancer reservieren?
Ja. Verwenden Sie die Annotation `loadbalancer.openstack.org/keep-floatingip`. Dann bleibt die Floating IP Ihrem Projekt zugeordnet, und der Kubernetes Service verwendet sie wieder.

Das folgende Beispiel zeigt einen Kubernetes Service mit einer reservierten IP-Adresse über diese Annotation:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-internet
  annotations:
    loadbalancer.openstack.org/keep-floatingip: "true"  # Annotation to keep the floating IP in the project
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
    - port: 80
      targetPort: 80
  loadBalancerIP: 45.94.8.9 # Specific floating IP to be reserved for the service
```

## Können wir einen Kubernetes Service mit einer bestimmten Floating IP erstellen?
Ja. Setzen Sie in der Service-Definition das Feld `loadBalancerIP`. Dann verwendet der Kubernetes Service eine bestimmte Floating IP. Die IP in `loadBalancerIP` muss in OpenStack bereits als Floating IP zugewiesen sein.

Das folgende Beispiel zeigt einen Kubernetes Service mit einer bestimmten Floating IP:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-internet
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 80
    targetPort: 80
  loadBalancerIP: 45.94.8.9 # Specific floating IP to be reserved for the service
```
Mit dem Feld `loadBalancerIP` verwendet der Service die angegebene Floating IP, wenn ein Load Balancer bereitgestellt wird.

## Kann ich external-dns und OpenStack Designate für automatisches DNS verwenden?

Ja. Dafür sind einige Installations- und Konfigurationsschritte nötig. Diese Schritte finden Sie [hier](/managedk8s/faq/automatic_dns).
