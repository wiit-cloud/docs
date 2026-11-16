---
title: Logging und Metriken
lang: "de"
permalink: /managedk8s/faq/logging_metrics/
nav_order: 4200
parent: FAQ
---
# Benutzerhandbuch für Logging und Metriken

Dieses Dokument gibt einen Überblick über die Funktionen für Logging und Metriken in unserem Managed-Kubernetes-Service. Bitte beachten Sie: Wir überwachen nur die Control Plane. Wir sammeln und speichern keine Anwendungsdaten der Benutzer und keine benutzergenerierten Logs aus den Workloads, die Kunden deployen.

Wir konzentrieren uns ausschließlich auf Metriken und Logs zum Zustand, zur Performance und zur betrieblichen Effizienz der Control Plane des Clusters.
Bitte beachten Sie, dass Sie auf alle Endpunkte für Logs und Metriken sogar von der Control Plane aus zugreifen können.


Kunden sind für die Verwaltung ihrer Anwendungslogs, Pod-Metriken und der Daten verantwortlich, die in ihren Workloads entstehen. Dazu gehört:

- Anwendungslogs sammeln: Kunden müssen möglicherweise ihre Logging-Lösungen (z. B. Fluentd, Filebeat) so konfigurieren, dass sie Logs aus den Anwendungen sammeln und an einem bevorzugten Ort speichern.
- Anwendungsmetriken überwachen: Für eigene Anwendungsmetriken können Kunden ihr eigenes Prometheus oder andere Monitoring-Lösungen einrichten.

Für die eigenständige Verwaltung von Anwendungslogs und -metriken empfehlen wir die folgenden Vorgehensweisen:

- Logging-Lösungen bereitstellen: Konfigurieren Sie Logging-Agenten (z. B. Fluentd, Logstash) auf jedem Node. Diese Agenten sammeln die Logs Ihrer Anwendungen und leiten sie an einen zentralen Logging-Service Ihrer Wahl weiter (z. B. Elasticsearch, Loki). Wir empfehlen, die Daten an unser [zentrales Logging-System Elastic](/ece/shipdata/) zu senden.

- Prometheus und Grafana einrichten: Für eine vollständige Metrik-Lösung stellen Sie Prometheus und Grafana bereit. Damit überwachen und visualisieren Sie Ressourcennutzung, eigene Metriken und die Performance Ihrer Anwendungen.

