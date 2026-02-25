---
title: Volume Type Spezifikationen
lang: "de"
permalink: /openstack/specs/volume_specification/
parent: Specifications
nav_order: 9101
last_modified_date: 2025-07-21
---

# Volumenspezifikationen

In OpenStack sind "Volumes" persistente Speicher, die Sie an Ihre laufenden OpenStack Compute-Instanzen anhängen können. In openstack haben wir drei Serviceklassen für Volumes eingerichtet. Diese haben unterschiedliche Limits, die unten aufgeführt sind.

## Volume-Typen

Wir haben zwei Hauptvolumentypen:

* high-iops
* standard

## Volumen-Typen-Liste

Nachfolgend eine Übersicht der zwei Volume-Typen:

| Name          | Read Bytes Sec | Read IOPS Sec  | Write Bytes Sec | Write IOPS Sec |
| :------------ | -------------: | -------------: | --------------: | -------------: |
| high-iops     | 524288000      | 10000          | 524288000       | 10000          |
| standard      | 209715200      | 2500           | 209715200       | 2500           |

## Auswählen eines Volume-Typs

Sie können beim Erstellen eines Volumes mit dem folgenden Befehl einen der zwei Volume-Typen auswählen (Wenn nicht anders angegeben, wird immer der Typ „Standard“ verwendet):
`$ openstack volume create <volume-name> --size 10 --type high-iops`

## Wechseln des Volume-Typs

Sie können den gewählten Typ eines Volumes anpassern, dazu könne Sie im Dashboard die Funktion "Change Voume Type" nutzen. Alternativ können sie den Typ über die CLI mit dem folgenden Command ändern:
`$ openstack volume set <volume-name> --type high-iops`
Eine Änderung des Volume Types ist jedoch nur möglich wenn das Volume nicht an einer Instanz in Verwendung ist.

## Volume-Gruppen Spezifikationen

In OpenStack sind "Volume-Gruppen" eine Möglichkeit, mehrere Volumes logisch zusammenzufassen. Dies vereinfacht die Verwaltung (z. B. das Erstellen von Snapshots für alle Volumes einer Gruppe) und hilft bei der Organisation komplexer Setups.

## Liste der Volume-Gruppentypen

Wir stellen aktuell den folgenden Gruppentyp bereit:

| Name | Beschreibung | Datenkonsistenz (Consistency) | Öffentlich (Public) |
| :--- | :--- | :--- | :--- |
| **standard_group** | Standard-Gruppierung für zusammengehörige Volumes. | Nein | Ja |

> **HINWEIS:** Der Typ standard_group bietet ausschließlich eine logische Gruppierung. Er garantiert jedoch keine Volume-übergreifende Konsistenz (Consistency Group). Snapshots werden nacheinander erstellt.

## Nutzung von Volume-Gruppen

### 1. Erstellen einer Volume-Gruppe

Um eine neue Gruppe zu erstellen, muss der Typ `standard_group` angegeben werden:

```bash
openstack volume group create --type standard_group <gruppen-name>
```

### 2. Volumes zu einer Gruppe hinzufügen

Ein Volume kann direkt beim Erstellen einer Gruppe zugewiesen werden:

```bash
openstack volume create --size 10 --group <gruppen-name> <volume-name>
```

### 3. Gruppen-Snapshots erstellen

Sie können einen gemeinsamen Snapshot-Punkt für alle Volumes in der Gruppe initiieren:

```bash
openstack volume group snapshot create --group <gruppen-name> <snapshot-name>
```
