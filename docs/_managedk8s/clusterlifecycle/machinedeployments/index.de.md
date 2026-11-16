---
title: Machine Deployments
lang: "de"
permalink: /managedk8s/clusterlifecycle/machinedeployments/
nav_order: 3500
parent: Cluster Lifecycle
---
# Machine Deployments

## Erstellung von Machine Deployments {#machine-deployment-creation}

- Wir erstellen die Machine Deployments nach Ihren Anforderungen.
- Wir konfigurieren jedes Machine Deployment für bestimmte Anforderungen, zum Beispiel Rechenkapazität, Speicher und Availability Zone.
- Bitte geben Sie den Namen der Machine Deployments an. Wenn Sie keinen Namen angeben, hat der Name das Standardformat `clusterName-az-md` (zum Beispiel cluster-test-az2-md).
- Wir können den Autoscaler für Sie aktivieren. Siehe [Cluster Autoscaler](/managedk8s/clusterlifecycle/autoscaling/).
- Ein Machine Health Check ersetzt Nodes, die dem Cluster nicht beitreten. Wir können eigene Checks hinzufügen. Dann ersetzt der Machine Health Check auch Nodes, die später fehlerhaft werden. Siehe [Machine Health Check](/managedk8s/clusterlifecycle/machinehealthcheck/).
- Wir können Pre-Cordoning (für den gesamten Cluster) aktivieren. Dann werden alle Nodes vor der Rotation gesperrt (cordoned). Details finden Sie unter [Node-Rotation](#node-rotation).
- Die Standardkonfiguration eines Machine Deployments ist:

```yaml
workers:
- name: clusterName-az-md
  replicas: 3
  autoscaler:
    min: 0
    max: 0
  availability_zones:
  - <random-az>
  roles:
  - "worker"
  restrictions: []
  taints: []
  machine_type: s1.large
  use_custom_disk: false
```

Wenn Sie andere Änderungen an der Konfiguration benötigen, nennen Sie diese bitte in Ihrer Anfrage für ein neues Machine Deployment. Lesen Sie aber zuerst die folgenden Konfigurationsdetails und Einschränkungen:

 * Flavor/Machine Type – der Standardtyp ist s1.large (`8 cores`, `16GB RAM` und `20GB` Disk-Größe). Sie können jeden Flavor wählen, der in Ihrem OpenStack-Projekt verfügbar ist. Stellen Sie aber sicher, dass der Flavor:
    * mindestens 2 Cores und 2 GB RAM hat
    * kein Windows-Image ist
    * genug Kapazität für Plattformkomponenten und Workloads bereitstellt. Für Ihre Anwendungs-Pods sind weniger Ressourcen verfügbar, als die Größe des gewählten Flavors angibt. Details finden Sie unter [Zuweisbare Ressourcen](/managedk8s/clusterlifecycle/allocatable-resources/).
* Anzahl der Replicas – der Standardwert ist 3 Nodes.
* Availability Zone (AZ) – geben Sie bei Bedarf eine der verfügbaren Zonen `az1`, `az2`, `az3` oder `sz1` an. Der Standardwert ist eine zufällige AZ.
  * Warnung: `sz1` ist eine spezielle Stretched Zone. Lesen Sie bitte [Stretched Zone (SZ1)](https://docs.wiit-cloud.io/openstack/intro/#stretched-zone-sz1), bevor Sie diese Zone wählen.
  * Sie dürfen `sz1` nicht mit anderen AZs im selben Machine Deployment kombinieren.
  * Wenn mehrere AZs konfiguriert sind, erstellen wir für jede AZ ein Machine Deployment mit der angegebenen Anzahl an Replicas. Beispiel: Sie wollen 3 Replicas mit den AZs az1 und az2. Dann gibt es 2 Machine Deployments mit je 3 Replicas.
* Eigene Root-Disk-Größe und/oder eigener Volume-Typ – weitere Informationen finden Sie unter [Root Disks](/managedk8s/clusterlifecycle/rootdisk/).
* Rollen werden als Labels auf den Nodes gesetzt. Die Labels haben das folgende Format: `node-role.kubernetes.io/<ROLENAME>: ""`
* Restrictions werden als Labels auf den Nodes gesetzt. Die Labels haben das folgende Format: `node-role.kubernetes.io/<RESTRICTIONNAME>: ""`

Beachten Sie bitte: Für Rollen und Restrictions akzeptieren wir nur dieses Format: `node-role.kubernetes.io/NAME: ""`

* Sie können Taints auf die Nodes eines Worker-Pools anwenden (`workers[].taints`). Jeder Taint benötigt einen `key` und einen `effect`. Ein `value` ist optional. Unterstützte Effects sind `NoSchedule`, `PreferNoSchedule` und `NoExecute`. Damit können Sie Worker-Pools für bestimmte Workloads reservieren, zum Beispiel GPU-Nodes. Beispiel:

  ```yaml
  workers:
  - name: <worker-pool-name>
    taints:
    - key: "nvidia.com/gpu"
      value: "present"
      effect: "NoSchedule"
  ```

## Änderungen an Machine Deployments {#machine-deployment-updates}

- Wir können das Machine Deployment für Sie ändern. Sie können:
  * den Flavor ändern
  * die Replicas ändern
  * die Application Credentials ändern
  * den Autoscaler aktivieren/deaktivieren
  * Taints hinzufügen, ändern oder entfernen

- Wir können neue Machine Deployments für Sie erstellen.
- Beachten Sie bitte:
  * Wir können die Availability Zone nicht ändern, da die Volumes in der alten Availability Zone bleiben.
  * Ein einzelnes Machine Deployment kann nicht mehrere Availability Zones umfassen. Wenn Sie mehrere Availability Zones benötigen, erstellen wir in jeder Availability Zone ein separates Machine Deployment.
  * Wir aktualisieren das Betriebssystem regelmäßig. So stellen wir sicher, dass die Nodes die neuesten Softwareversionen, Patches und Sicherheitsupdates verwenden.

## Node-Rotation {#node-rotation}

Wir rotieren die Nodes in Ihrem Kubernetes-Cluster. So stellen wir optimale Leistung, Sicherheit und Zuverlässigkeit sicher.
Wir rotieren die Nodes aller Machine Deployments gleichzeitig. Der Prozess ist:

* Sperren (Cordoning) der alten Nodes: Wenn konfiguriert, werden zuerst alle Nodes im Machine Deployment gesperrt (nur bei einem Image-Update).
* Erstellen neuer Nodes: Ein neuer Node wird erstellt und ersetzt einen alten Node.
* Leeren (Draining) der alten Nodes: Wir leeren jeden alten Node sicher. Dabei werden alle laufenden Pods kontrolliert verdrängt (evicted).
* Löschen der alten Nodes: Wenn der neue Node voll funktionsfähig ist und alle Workloads fehlerfrei laufen, wird der alte Node gelöscht.

Dieser Prozess gilt für alle Nodes in den Machine Deployments des Clusters. Wir führen ihn regelmäßig oder bei Bedarf aus, um den gewünschten Zustand des Clusters zu erhalten.

Die Node-Rotation ist aus mehreren Gründen notwendig, unter anderem:

**Sicherheitsupdates:** Kritische Sicherheitspatches und Updates für das Betriebssystem einspielen.

**Leistungsverbesserungen:** Upgrade auf neuere Kubernetes-Versionen.

**Ressourcenoptimierung:** Anpassen der Node-Konfigurationen.

## Ihre Aufgaben {#what-you-need-to-take-care-of}

**Stabilität der Workloads:** Stellen Sie sicher, dass Ihre Anwendungen mögliche Unterbrechungen tolerieren. Kubernetes versucht, Ihre Workloads automatisch neu einzuplanen. Bei manchen Anwendungen kann es aber zu kurzen Ausfallzeiten kommen, oder sie benötigen einen manuellen Eingriff.

**Pod Disruption Budgets:** Prüfen Sie Ihre Pod Disruption Budgets (PDBs) und aktualisieren Sie sie bei Bedarf. Mit PDBs steuern Sie, wie viele Pods während der Rotation sicher verdrängt werden können, ohne die Verfügbarkeit der Anwendung zu beeinträchtigen.

Mit diesen Schritten helfen Sie, eine reibungslose Node-Rotation mit minimalen Auswirkungen auf Ihre Services sicherzustellen. Wenn Sie Fragen haben oder weitere Unterstützung benötigen, kontaktieren Sie uns bitte.
