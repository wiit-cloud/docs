---
title: Key Manager
lang: "de"
permalink: /openstack/keymanager/
has_children: true
nav_order: 4000
---

# Key Manager

Der Key-Manager-Dienst (Barbican) stellt sicheren Speicher für Geheimnisse wie Passwörter, private Schlüssel und TLS-Zertifikate bereit. Secrets werden verschlüsselt abgelegt und sind auf Ihr Projekt beschränkt — andere Projekte können sie nicht lesen.

Typische Anwendungsfälle:

* [Verschlüsselte Volumes](/openstack/keymanager/encrypted_volumes/) — die Schlüssel für verschlüsselte Cinder-Volume-Typen werden automatisch im Key Manager erzeugt und hinterlegt
* [TLS-terminierte Loadbalancer](/openstack/keymanager/tls_termination/) — laden Sie Ihr Zertifikat einmal hoch und referenzieren es auf Octavia-Listenern
* [Generische Secrets](/openstack/keymanager/secrets/) — hinterlegen Sie Passphrasen oder Schlüssel und lesen Sie sie über die API aus Ihren Anwendungen

## Zugriff

Secrets lassen sich über die OpenStack-CLI und die Dashboards verwalten:

```bash
(openstack) $ pip install python-barbicanclient
(openstack) $ openstack secret list
```

Dieselben Operationen stehen in Horizon und Skyline unter **Key Manager** zur Verfügung.

## Einschränkungen

* Secrets können nach dem Erstellen nicht verändert werden — eine Änderung bedeutet löschen und neu anlegen.
* Orders (serverseitige Erzeugung asymmetrischer Schlüssel und Zertifikate) werden nicht unterstützt.
* Das Löschen eines Secrets ist endgültig. Bei Volume-Verschlüsselungsschlüsseln bedeutet das, dass die Daten auf den betroffenen Volumes nicht wiederherstellbar sind.
