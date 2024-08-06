---
title: Release Notes
lang: "de"
permalink: /edge/operationscenter/release_notes/
has_children: false
nav_order: 2100
parent: Operations Center
---

# Release Notes

## 1.5.3
    Fehlerbehebung:
        - VPNaaS widerrufene Zertifikate wurden nicht korrekt an die CRL angehängt
    Features:
        - Passwort Generator und Tooltip für VM Passwörter hinzugefügt
    Verbesserungen:
        - Anlegen externer User mit frauenhofer.de Email blockiert und Tooltip hierfür hinzugefügt
        - VPNaas Zertifikates Gültigkeit auf 10 Jahre erhöht
        - Limitierung der Projektnamen länge um VPNaaS Probleme zu vermeiden
        - Update der VPNaaS Version um für die Unterstützung neuer OpenVPN clients
        - Refresh Button in der Resource Overview  ruf IP und Status Information der VMs ab
        - Verbesserte Backup Performance

## 1.5.4
    Features:
        Flavor für VMs können für das Operations Center verborgen werden.
        Um einen Flavor zu verbergen muss im Openstack die Metadata mit einen Custom Feld visibility ergänzt und dieses auf false gesetzt werden.
        Der Flavor ist jetzt nicht mehr in Operations Center sichtbar.
        Hinweis: Der Flavor ist nur in der Auswahl für neue VMs verborgen. VMs dieden Flavor bereits nutzen sind davon nicht beeinflusst und können weiterhin im Operations Center verwaltet werden.


## 1.5.6
    Änderungen Resource Overview:
        Fehlerbehebung:
            - Ports werden jetzt alle im Tooltip angezeigt und nicht mehr gekürzt
        Features:
            - IP zeigt nun sowohl interne als auch floating IP an

## 1.5.9
    Verbesserungen:
        - Updates für den Backend Ingress

## 1.5.11
    Fehlerbehebung:
        - Backups sind nicht mehr wegen alte VMs mit Leerzeichen am Anfang blockiert
        - Limitierung von Projektnamen funktioniert jetzt wie vorgesehen
