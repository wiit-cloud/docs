---
title: Wartungsfenster
lang: "de"
permalink: /managedk8s/clusterlifecycle/maintenancewindow/
nav_order: 3320
parent: Cluster Lifecycle
---
# Wartungsfenster

Damit der Cluster sicher und aktuell bleibt, können verschiedene Arten von Updates stattfinden:

* Kubernetes-Patch-Version
* Flatcar OS
* Installierte Apps (z. B. CNI/CPI)
* System

Einige dieser Updates lösen eine Node-Rotation aus (Updates von Flatcar oder Kubernetes-Patch-Versionen), andere nicht (Updates von Apps). Ihre Anwendungen müssen deshalb jederzeit mit Node-Rotationen umgehen können.

Wenn kein Wartungsfenster konfiguriert ist, können Updates jederzeit stattfinden. Damit Sie mehr Kontrolle über den Zeitpunkt der Updates haben, haben wir ein Wartungsfenster implementiert.

## Das Fenster

Um Wartungsfenster zu definieren, können wir dem Cluster mehrere Zeitbereiche pro Tag hinzufügen. **Wir benötigen aber mindestens ein Fenster von 1 Stunde pro Woche!**
So sammeln sich nicht zu viele ausstehende Änderungen an, und wir können sicher sein, dass alle Änderungen mindestens einmal pro Woche angewendet werden.

```
<day of week>: <start time>-<end time>, <start time>-<end time>, ...

Mon: 06:00-10:00, 17:00-20:00
```

Einige wichtige Fälle:
* Eine begonnene Wartung wird immer abgeschlossen, auch wenn sie länger dauert als das konfigurierte Fenster.
* Wenn Sie eine Änderung an Ihrem Cluster machen (z. B. mehr Replicas, neue Minor-Version usw.), werden alle ausstehenden Änderungen gleichzeitig angewendet.

**In seltenen Fällen, bei Breaking Changes oder sehr wichtigen CVEs, können wir kurzfristig eine globale Wartung planen.**
