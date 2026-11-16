---
title: OpenStack Application Credentials
lang: "de"
permalink: /managedk8s/clusterlifecycle/appcredentials/
nav_order: 3300
parent: Cluster Lifecycle
---

Für einen verwalteten Kubernetes-Cluster auf OpenStack benötigen wir ein Paar OpenStack Application Credentials. Damit stellen wir den korrekten Zugriff im OpenStack-Projekt sicher. Folgen Sie der [OpenStack-Dokumentation](/openstack/quickstart/application_credentials/), um diese Credentials sicher zu erstellen und zu verwalten.

Uneingeschränkte (unrestricted) Application Credentials sind nicht notwendig. Eingeschränkte (restricted) Application Credentials sind ausreichend.

WIIT stellt keine etcd-Backups für verwaltete Kubernetes-Cluster bereit. Planen Sie eigene Backups für die Workloads und Daten in Ihrem Cluster. WIIT führt auf jedem Cluster täglich eine etcd-Defragmentierung aus.

Beachten Sie bitte:
 - Senden Sie die Credentials nicht über unsichere Kanäle. Verwenden Sie nur die Tools und Methoden, die beim Onboarding vereinbart wurden.
 - Verknüpfen Sie Application Credentials mit einem dedizierten Service-Account, nicht mit einem persönlichen Benutzerkonto. So vermeiden Sie Probleme, wenn die Credentials beim Offboarding eines Benutzers widerrufen werden.
 - Bei Bedarf können wir die Application Credentials im Cluster rotieren.
