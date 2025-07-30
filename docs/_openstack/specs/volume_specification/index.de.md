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

Wir haben drei Hauptvolumentypen:

* high-iops
* standard

## Volumen-Typen-Liste

Nachfolgend eine Übersicht der drei Volume-Typen:

| Name          | Read Bytes Sec | Read IOPS Sec  | Write Bytes Sec | Write IOPS Sec |
| :------------ | -------------: | -------------: | --------------: | -------------: |
| high-iops     | 524288000      | 10000          | 524288000       | 10000          |
| standard      | 209715200      | 2500           | 209715200       | 2500           |

## Auswählen eines Volume-Typs

Sie können beim Erstellen eines Volumes mit dem folgenden Befehl einen der drei Volume-Typen auswählen (Wenn nicht anders angegeben, wird immer der Typ „Standard“ verwendet):
`$ openstack volume create <volume-name> --size 10 --type high-iops`

## Wechseln des Volume-Typs

Sie können den gewählten Typ eines Volumes anpassern, dazu könne Sie im Dashboard die Funktion "Change Voume Type" nutzen. Alternativ können sie den Typ über die CLI mit dem folgenden Command ändern:
`$ openstack volume set <volume-name> --type high-iops`
Eine Änderung des Volume Types ist jedoch nur möglich wenn das Volume nicht an einer Instanz in Verwendung ist.
