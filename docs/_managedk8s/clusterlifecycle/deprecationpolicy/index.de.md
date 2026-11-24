---
title: Abkündigungsrichtlinie
lang: "de"
permalink: /managedk8s/clusterlifecycle/deprecationpolicy/
nav_order: 3700
parent: Cluster Lifecycle
---

# Abkündigungsrichtlinie

Das Upstream-Projekt Kubernetes veröffentlicht ungefähr drei Kubernetes-Versionen pro Jahr. Es kündigt jedes Jahr ungefähr gleich viele alte Versionen ab.
Kubernetes folgt einer N-2-Support-Richtlinie. Das bedeutet: Die 3 neuesten Minor-Versionen erhalten Sicherheits- und Fehlerkorrekturen.


| Version     | Supportende    | Status                      |
| ----------- | -------------- | --------------------------- |
| 1.35        | 2027-02-28     | aktuell verfügbare Version  |
| 1.34        | 2026-10-27     | aktuell verfügbare Version  |
| 1.33        | 2026-06-28     | aktuell verfügbare Version  |
| 1.32        | 2026-02-28     | abgekündigt                 |
| 1.31        | 2025-10-28     | abgekündigt                 |
| 1.30        | 2025-06-28     | abgekündigt                 |
| 1.29        | 2025-02-28     | abgekündigt                 |


Managed Kubernetes orientiert sich lose an diesem Lebenszyklus. Wir führen laufend neue Versionen ein und kündigen ältere Versionen ab.

Wenn eine Kubernetes-Version ihr End-of-Life erreicht hat, erhält sie keine Fehlerkorrekturen und keine Sicherheitsupdates mehr. Darum können auch wir diese Version nicht mehr unterstützen und müssen sie abkündigen.

## Abkündigungsprozess

Wenn wir eine bestimmte Kubernetes-Version abkündigen, informieren wir Sie frühzeitig. Die Benachrichtigung enthält eine End-of-Life-Ankündigung mit dem Zeitplan der Abkündigung. Planen und bereiten Sie in diesem Zeitraum das Upgrade Ihres Clusters auf eine unterstützte Kubernetes-Version vor. Das Upgrade muss erfolgen, bevor wir die abgekündigte Version entfernen.

Die Liste der unterstützten Kubernetes-Versionen und ihre geplanten End-of-Life-Daten finden Sie [hier](/managedk8s/about/kubernetesversions/).

**Was bedeutet eine End-of-Life-Ankündigung für mich?**

Wenn es für eine bestimmte Kubernetes-Version eine End-of-Life-Ankündigung gibt, empfehlen wir: Kontaktieren Sie uns als Kunde, damit wir Ihre Cluster auf eine neuere Version aktualisieren, vorzugsweise auf die neueste Version.

**Was passiert, wenn ich nicht vor dem EOL-Datum aktualisiere?**

Nach Ablauf des EOL-Zeitraums gilt:

- Die EOL-Kubernetes-Version ist im Managed Service nicht mehr verfügbar.
- Alle Cluster, die noch die EOL-Version ausführen, erhalten automatisch ein Upgrade auf eine unterstützte Version.
- Neue Cluster können nicht mit der EOL-Version erstellt werden.

**Kann ich für immer auf einer EOL-Version bleiben?**

Nein. Das könnte in Zukunft zu schweren Sicherheitsproblemen führen.

## Richtlinie für erzwungene Upgrades

Wenn eine Kubernetes-Version ihr End-of-Life erreicht, müssen wir den Support für diese Version aus Managed Kubernetes entfernen. Die Version erhält dann keine Fehlerkorrekturen und keine Sicherheitsupdates mehr.

Beachten Sie besonders die folgenden technischen Einschränkungen in Kubernetes:

* Ein Upgrade eines Kubernetes-Clusters (genauer: seiner Control Plane) ist immer nur um eine Minor-Version möglich, z. B. von v1.30 auf v1.31.
* Ein Upgrade um mehr als eine Version auf einmal ist nicht möglich.
* Ein Downgrade eines Clusters ist nicht möglich.

Das bedeutet: Wenn Kunden ihre Cluster nicht aktualisieren, bevor wir eine EOL-Version entfernen, riskieren sie Folgendes: Sobald wir die **nächste** abgekündigte Version entfernen, ist möglicherweise gar kein Upgrade mehr möglich.

Das wäre ein schweres Problem. Die einzige Alternative wäre dann, einen neuen Cluster zu erstellen und den Workload vom alten Cluster zu migrieren. Ein Upgrade wäre nicht möglich, weil es ein Upgrade um zwei Versionen auf einmal erfordern würde.

Um dieses Problem zu vermeiden, müssen wir Kunden aktiv zu einem Upgrade zwingen. Das betrifft Cluster mit einer EOL-Kubernetes-Version. Das Upgrade muss erfolgen, bevor wir die nächste abgekündigte Version entfernen.

**Was passiert mit meinen Clustern bei einem erzwungenen Upgrade?**

Wir starten ein automatisches Kubernetes-Upgrade für die Control Plane und das oder die Machine Deployment(s).

Das Upgrade *sollte* funktionieren. Wegen der Vielfalt von Anwendungen und Anwendungsfällen können wir das aber nicht garantieren.

Breaking Changes in der Kubernetes API können zu defekten oder inkompatiblen Anwendungen im Kubernetes-Cluster führen. **Für solche Probleme können wir keine Verantwortung übernehmen**.

Für optimale Leistung und Sicherheit empfehlen wir allen Kunden dringend, ihre Kubernetes-Cluster aktuell zu halten. Bitte kontaktieren Sie uns, um die Upgrades Ihrer Cluster zu planen.
