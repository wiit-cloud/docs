---
title: Glance-Image herunterladen
lang: "de"
permalink: /edge/openstack/download_glance_image/
has_children: false
nav_order: 3200
parent: Openstack
---

# Glance-Image mit OpenRC herunterladen

Diese Anleitung zeigt:

1. OpenStack OpenRC Datei in Horizon herunterladen.
2. OpenRC Anmeldedaten lokal laden.
3. Glance-Image mit `openstack image save` herunterladen.

## Voraussetzungen

- Horizon Benutzer mit Zugriff auf das Zielprojekt.
- OpenStack CLI lokal installiert.
- Für eine detaillierte Installation der CLI siehe die OpenStack Dokumentation: [Install OpenStack command-line clients](https://docs.openstack.org/newton/user-guide/common/cli-install-openstack-command-line-clients.html).
- Unter macOS kannst du die CLI mit Homebrew installieren: `brew install openstackclient`.
- Unter Windows verwende bitte WSL und führe dieselben Shell-Befehle wie unter Linux/macOS aus.
- Ausreichend lokaler Speicherplatz für die Image-Datei.

## 1. OpenRC Datei aus Horizon herunterladen

1. In Horizon anmelden.
2. Projekt öffnen.
3. Oben rechts im Benutzer-Menü auf den Benutzernamen klicken.
4. `OpenStack RC File` auswählen.
5. Die Projekt OpenRC Datei herunterladen (normalerweise `openrc.sh`).

## 2. OpenRC lokal laden (alle OS-Typen)

### 2.1 OpenRC Datei ablegen

Öffne eine Shell (Linux Terminal, macOS Terminal oder WSL unter Windows) und verschiebe die heruntergeladene Datei in dein Arbeitsverzeichnis, zum Beispiel:

```bash
mv ~/Downloads/openrc.sh ~/openstack/openrc.sh
cd ~/openstack
```

### 2.2 OpenRC Datei laden

```bash
source ./openrc.sh
```

Wenn abgefragt, das OpenStack Passwort eingeben.

### 2.3 Authentifizierung prüfen

```bash
openstack token issue
```

### 2.4 Images auflisten und Image-ID kopieren

```bash
openstack image list
```

### 2.5 Image herunterladen

```bash
openstack image save --file my-image.qcow2 <IMAGE_ID_OR_NAME>
```

Hinweis: Verwende `.qcow2` für `qcow2` Images und `.raw` für `raw` Images. Das Image-Format kannst du so prüfen:

```bash
openstack image show <IMAGE_ID_OR_NAME> -f value -c disk_format
```

Beispiel:

```bash
openstack image save --file ubuntu-22.04.qcow2 ubuntu-22.04
```

## 3. Fehlerbehebung

- `The resource could not be found.`
  - Image-Name oder ID mit `openstack image list` prüfen.
- `Missing value auth-url required for auth plugin password`
  - OpenRC Variablen wurden nicht korrekt geladen.
- `HTTP 401 Unauthorized`
  - Passwort erneut eingeben und Projekt/User Domain Werte prüfen.
