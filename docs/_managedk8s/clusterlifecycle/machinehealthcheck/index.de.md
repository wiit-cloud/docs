---
title: Machine Health Check
lang: "de"
permalink: /managedk8s/clusterlifecycle/machinehealthcheck/
nav_order: 3505
parent: Cluster Lifecycle
---
# Machine Health Check

Ein Machine Health Check erkennt fehlerhafte Worker Nodes in Ihrem Cluster automatisch und ersetzt sie. Niemand muss einen defekten Node bemerken und manuell reparieren. Die Plattform löscht die fehlerhafte Maschine, und das Machine Deployment erstellt eine neue Maschine mit derselben Konfiguration (Flavor, Availability Zone, Root Disk, Labels und Taints).

Machine Health Checks werden pro Machine Deployment konfiguriert und gelten nur für Worker Nodes. Die Control Plane überwachen und reparieren wir separat.

# Funktionsweise eines Machine Health Check {#how-a-machine-health-check-works}

Für jedes Machine Deployment ist ein Machine Health Check aktiv. Sie müssen ihn nicht aktivieren.

**Erkennung:**
Ohne weitere Konfiguration gilt eine Maschine als fehlerhaft, wenn:

- Eine neu erstellte Maschine nicht innerhalb von **10 Minuten** dem Cluster als Node beitritt. Ursachen sind zum Beispiel: Die OpenStack-Instanz kann nicht erstellt werden, oder der Node kann sich nicht beim Cluster registrieren. Das Zeitlimit von 10 Minuten verhindert, dass Nodes unnötig ersetzt werden, die beim Start langsamer als üblich sind.
- Der Node der Maschine aus dem Cluster gelöscht wurde.

Beide Prüfungen sind immer aktiv. Sie können sie nicht ändern oder deaktivieren. Sie können eigene Prüfungen hinzufügen, um auch Nodes zu ersetzen, die später im Betrieb fehlerhaft werden, zum Beispiel wenn ein Node `Ready=Unknown` meldet (siehe [Eigene Konfiguration](#custom-configuration)).

**Behebung:**
Wenn eine Maschine als fehlerhaft markiert ist, wird sie gelöscht, und das Machine Deployment erstellt eine Ersatzmaschine. Der neue Node tritt dem Cluster bei und steht für das Scheduling zur Verfügung. Kubernetes plant die Pods, die auf dem fehlerhaften Node liefen, auf den verbleibenden fehlerfreien Nodes neu ein.

**Schutzmechanismus:**
Standardmäßig wird pro Machine Deployment nur **eine Maschine gleichzeitig** ersetzt. Wenn viele Nodes gleichzeitig fehlerhaft werden, zum Beispiel bei einer kurzen Netzwerkstörung, verhindert dies, dass alle Maschinen gleichzeitig neu erstellt werden.

# Standardkonfiguration

Ohne weitere Einstellungen gilt für jedes Machine Deployment die folgende Konfiguration für die Behebung. Es sind keine zusätzlichen Prüfungen konfiguriert:

```yaml
workers:
- name: <machine-deployment-name>
  healthCheck:
    remediation:
      maxInFlight: 1
```

# Eigene Konfiguration {#custom-configuration}

Sie können eigene Prüfungen hinzufügen und die Einstellungen für die Behebung ändern. Ihre Prüfungen kommen zu den Prüfungen hinzu, die in [Funktionsweise eines Machine Health Check](#how-a-machine-health-check-works) beschrieben sind. Sie ersetzen oder deaktivieren diese Prüfungen nie. Ihre Einstellungen für die Behebung ersetzen den Standardwert `maxInFlight: 1` (siehe [Eigene Einstellungen mit den Standardwerten kombinieren](#combining-custom-settings-with-the-defaults)). Kontaktieren Sie uns dazu wie [hier](/managedk8s/about/support/) beschrieben.

Geben Sie bei Ihrer Anfrage die folgenden Informationen an:

- [WIIT Resource Name](/managedk8s/about/support/#wiit-resource-name)
- Den Namen oder die Namen der Machine Deployments, für die die Einstellungen gelten sollen
- Die Prüfungen und Einstellungen für die Behebung, die Sie benötigen

Wählen Sie die Zeitlimits sorgfältig. Zu kurze Werte können dazu führen, dass Nodes bei kurzen Störungen ersetzt werden, die sich selbst beheben. Zu lange Werte lassen defekte Nodes länger im Cluster.

## Prüfungen {#checks}

Der Block `checks` definiert zusätzliche Bedingungen, unter denen eine Maschine als fehlerhaft gilt. Nur die Bedingungen, die Sie angeben, werden geprüft. Es werden keine anderen Bedingungen hinzugefügt. Die Prüfungen aus [Funktionsweise eines Machine Health Check](#how-a-machine-health-check-works) bleiben in jedem Fall aktiv:

| Option | Steuert |
|---|---|
| `unhealthyNodeConditions` | Liste von Bedingungen auf dem Kubernetes-Node, zum Beispiel `Ready`, `MemoryPressure`, `DiskPressure` oder `PIDPressure`. |
| `unhealthyMachineConditions` | Liste von Bedingungen auf der Maschine, zum Beispiel `NodeHealthy` oder `InfrastructureReady`. |

Jede Bedingung besteht aus einem `type`, einem `status` (`True`, `False` oder `Unknown`) und einem `timeoutSeconds`. Eine Maschine gilt als fehlerhaft, sobald **eine beliebige** der Bedingungen mindestens für ihr Zeitlimit den angegebenen Status hat. Jede Bedingung hat ihr eigenes Zeitlimit. Sie können also zum Beispiel schneller auf `Ready=Unknown` reagieren als auf `DiskPressure=True`.

### Verfügbare Node-Bedingungen {#available-node-conditions}

Das Kubelet auf jedem Node meldet diese Bedingungen. Sie sehen sie mit `kubectl describe node <node-name>`.

| Typ | Fehlerfreier Status | Fehlerhafter Status | Bedeutung |
|---|---|---|---|
| `Ready` | `True` | `False`, `Unknown` | Der Node ist fehlerfrei und bereit, Pods anzunehmen. `Unknown` bedeutet, dass der Node seinen Status nicht mehr meldet, zum Beispiel weil das Kubelet ausgefallen ist oder der Node seine Netzwerkverbindung verloren hat. |
| `MemoryPressure` | `False` | `True` | Der Node hat wenig freien Arbeitsspeicher. |
| `DiskPressure` | `False` | `True` | Der Node hat wenig freien Speicherplatz. |
| `PIDPressure` | `False` | `True` | Auf dem Node laufen zu viele Prozesse. |
| `NetworkUnavailable` | `False` | `True` | Das Netzwerk des Nodes ist nicht korrekt konfiguriert. |

### Verfügbare Maschinen-Bedingungen {#available-machine-conditions}

Die Plattform meldet diese Bedingungen für jede Maschine. Sie sind innerhalb Ihres Clusters nicht sichtbar.

| Typ | Fehlerfreier Status | Fehlerhafter Status | Bedeutung |
|---|---|---|---|
| `InfrastructureReady` | `True` | `False` | Die OpenStack-Instanz der Maschine wurde erfolgreich erstellt und läuft. `False` bedeutet zum Beispiel, dass die Instanz in einen Fehlerzustand gewechselt ist. Diese Bedingung kann Probleme erkennen, bevor der Node dem Cluster beitritt. |
| `BootstrapConfigReady` | `True` | `False` | Die Konfiguration, mit der die Maschine als Kubernetes-Node eingerichtet wird, wurde erfolgreich erzeugt. |
| `NodeReady` | `True` | `False`, `Unknown` | Der Node der Maschine ist bereit. Diese Bedingung spiegelt die Bedingung `Ready` des Nodes. |
| `NodeHealthy` | `True` | `False`, `Unknown` | Der Node der Maschine ist bereit und meldet kein `MemoryPressure`, `DiskPressure` oder `PIDPressure`. |

Andere Maschinen-Bedingungen, zum Beispiel `Ready`, `Available` oder `UpToDate`, beschreiben den Lebenszyklus einer Maschine und nicht ihren Zustand. Zum Beispiel ist `UpToDate` bei jeder [Node Rotation](/managedk8s/clusterlifecycle/machinedeployments/#node-rotation) `False`. Verwenden Sie diese Bedingungen nicht für Health Checks. Sonst können fehlerfreie Maschinen ersetzt werden.

Beispiel: Nodes ersetzen, die 5 Minuten lang nicht bereit sind oder 30 Minuten lang `DiskPressure` melden:

```yaml
workers:
- name: <machine-deployment-name>
  healthCheck:
    checks:
      unhealthyNodeConditions:
      - type: Ready
        status: "False"
        timeoutSeconds: 300
      - type: Ready
        status: Unknown
        timeoutSeconds: 300
      - type: DiskPressure
        status: "True"
        timeoutSeconds: 1800
```

## Behebung {#remediation}

Der Block `remediation` definiert, wie viele Maschinen ersetzt werden und unter welchen Umständen:

| Option | Steuert |
|---|---|
| `maxInFlight` | Wie viele Maschinen gleichzeitig ersetzt werden können, als absolute Zahl (z. B. `2`) oder als Prozentsatz der gewünschten Replicas (z. B. `20%`). Ohne diesen Wert begrenzt nur die Anzahl der Replicas die Anzahl paralleler Ersetzungen. |
| `triggerIf.unhealthyLessThanOrEqualTo` | Maschinen werden nur ersetzt, wenn die Anzahl fehlerhafter Maschinen kleiner oder gleich diesem Wert ist, als absolute Zahl (z. B. `2`) oder als Prozentsatz (z. B. `40%`). Ohne diesen Wert werden Maschinen ersetzt, unabhängig davon, wie viele fehlerhaft sind. |
| `triggerIf.unhealthyInRange` | Maschinen werden nur ersetzt, wenn die Anzahl fehlerhafter Maschinen im angegebenen Bereich liegt, z. B. `[3-5]`. Wenn beide Werte gesetzt sind, hat dieser Wert Vorrang vor `unhealthyLessThanOrEqualTo`. |

Die Optionen unter `triggerIf` wirken als Schutzschalter (Circuit Breaker). Wenn zu viele Maschinen gleichzeitig fehlerhaft sind, deutet das meist auf ein größeres Problem hin, zum Beispiel einen Ausfall des Netzwerks oder einer Availability Zone. In diesem Fall hilft das Ersetzen von Maschinen nicht. Es werden dann keine Maschinen ersetzt, bis die Anzahl fehlerhafter Maschinen wieder innerhalb des konfigurierten Limits liegt.

So werden die Limits ausgewertet:

- **Absolute Zahl:** Mit `unhealthyLessThanOrEqualTo: 2` werden bis zu 2 fehlerhafte Maschinen ersetzt. Wenn 3 oder mehr Maschinen fehlerhaft sind, wird keine ersetzt, unabhängig von der Größe des Machine Deployments.
- **Prozentsatz:** Mit `unhealthyLessThanOrEqualTo: 40%` und 25 Maschinen werden bis zu 10 fehlerhafte Maschinen ersetzt; mit 6 Maschinen bis zu 2. Nicht ganzzahlige Ergebnisse werden abgerundet. In Machine Deployments mit nur wenigen Replicas entspricht schon eine einzige fehlerhafte Maschine einem großen Prozentsatz.
- **Bereich:** Mit `unhealthyInRange: "[3-5]"` werden Maschinen nur ersetzt, wenn 3, 4 oder 5 Maschinen fehlerhaft sind. Bei 2 oder weniger oder bei 6 oder mehr fehlerhaften Maschinen wird keine ersetzt.

Beispiel: Nodes ersetzen, die 10 Minuten lang nicht bereit sind, höchstens eine Maschine gleichzeitig ersetzen und keine Maschinen mehr ersetzen, wenn mehr als 40 % des Machine Deployments fehlerhaft sind:

```yaml
workers:
- name: <machine-deployment-name>
  healthCheck:
    checks:
      # Node conditions, combined with a logical OR
      unhealthyNodeConditions:
      - type: Ready
        status: "False"
        timeoutSeconds: 600
      - type: Ready
        status: Unknown
        timeoutSeconds: 600
      # Machine conditions, combined with a logical OR
      unhealthyMachineConditions:
      - type: NodeHealthy
        status: "False"
        timeoutSeconds: 600
    remediation:
      # Replace at most one machine at a time
      maxInFlight: 1
      triggerIf:
        # Only replace machines if at most 40% of the machine deployment is unhealthy
        unhealthyLessThanOrEqualTo: 40%
        # Alternatively, only replace machines if 1 to 3 machines are unhealthy.
        # Takes precedence over unhealthyLessThanOrEqualTo if both are set.
        # unhealthyInRange: "[1-3]"
```

## Eigene Einstellungen mit den Standardwerten kombinieren {#combining-custom-settings-with-the-defaults}

Eigene Einstellungen für die Behebung werden nicht mit dem Standardwert zusammengeführt. Wenn Sie `remediation` angeben, auch nur teilweise, wird der Standardwert `maxInFlight: 1` vollständig ersetzt. Ein Beispiel: Wenn das obige Beispiel für die Behebung `maxInFlight: 1` nicht setzen würde, könnten beliebig viele Maschinen gleichzeitig ersetzt werden. Wenn Sie nur ein Limit mit `triggerIf` hinzufügen möchten, geben Sie bitte auch `maxInFlight` an.

# Zustand der Nodes prüfen {#checking-node-health}

Mit kubectl sehen Sie den Zustand Ihrer Nodes:

```bash
# List all nodes and their status
kubectl get nodes

# Show the conditions of a specific node
kubectl describe node <node-name>
```

Ein Node, der gerade ersetzt wird, hat den Status `NotReady`, bis er aus dem Cluster entfernt wird. Kurz danach erscheint ein neuer Node mit einem anderen Namen in der Liste.

Wenn Sie Fragen haben oder weitere Unterstützung benötigen, [kontaktieren Sie uns](/managedk8s/about/support/).
