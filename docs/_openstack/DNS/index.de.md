---
title: Designate DNSaaS 
lang: "de"
permalink: /openstack/DNS/
nav_order: 8999
---

# Anlegen eines DNS-Record in Designate

## Vorwort

Die Openstack Plattform bietet DNS-as-a-Service (DNSaaS), in Openstack auch bekannt als Designate.
DNSaaS enthält eine REST-API für die Domänen- und Datensatzverwaltung, ist multi-tenant und integriert den OpenStack Identity Service (Keystone) für die Authentifizierung.

Wir werden in diesem Schritt eine fiktive Zone (Domain) mit MX und A-Records erstellen und die entsprechende IP/CNAME hinterlegen.

-----

Zu Beginn sorgen wir dafür, dass der `python-designateclient` installiert ist (pip install python-openstackclient python-designateclient)
Anschliessend erstellen wir eine Zone innerhalb des Projekts:

```bash
$ openstack zone create --email webmaster@foobar.cloud foobar.cloud.
+----------------+--------------------------------------+
| Field          | Value                                |
+----------------+--------------------------------------+
| action         | CREATE                               |
| attributes     |                                      |
| created_at     | 2018-08-15T06:45:24.000000           |
| description    | None                                 |
| email          | webmaster@foobar.cloud               |
| id             | 036ae6e6-6318-47e1-920f-be518d845fb5 |
| masters        |                                      |
| name           | foobar.cloud.                        |
| pool_id        | bb031d0d-b8ca-455a-8963-50ec70fe57cf |
| project_id     | 2b62bc8ff48445f394d0318dbd058967     |
| serial         | 1534315524                           |
| status         | PENDING                              |
| transferred_at | None                                 |
| ttl            | 3600                                 |
| type           | PRIMARY                              |
| updated_at     | None                                 |
| version        | 1                                    |
+----------------+--------------------------------------+
```

Man beachte den abschliessenden "." an der zu erstellenden Zone/Domain.
Das Resultat bisher:

```bash
$ openstack zone list
+--------------------------------------+-----------------------+---------+------------+--------+--------+
| id                                   | name                  | type    |     serial | status | action |
+--------------------------------------+-----------------------+---------+------------+--------+--------+
| 036ae6e6-6318-47e1-920f-be518d845fb5 | foobar.cloud.         | PRIMARY | 1534315524 | ACTIVE | NONE   |
+--------------------------------------+-----------------------+---------+------------+--------+--------+

$ openstack zone show foobar.cloud.
+----------------+--------------------------------------+
| Field          | Value                                |
+----------------+--------------------------------------+
| action         | NONE                                 |
| attributes     |                                      |
| created_at     | 2018-08-15T06:45:24.000000           |
| description    | None                                 |
| email          | webmaster@foobar.cloud               |
| id             | 036ae6e6-6318-47e1-920f-be518d845fb5 |
| masters        |                                      |
| name           | foobar.cloud.                        |
| pool_id        | bb031d0d-b8ca-455a-8963-50ec70fe57cf |
| project_id     | 2b62bc8ff48445f394d0318dbd058967     |
| serial         | 1534315524                           |
| status         | ACTIVE                               |
| transferred_at | None                                 |
| ttl            | 3600                                 |
| type           | PRIMARY                              |
| updated_at     | 2018-08-15T06:45:30.000000           |
| version        | 2                                    |
+----------------+--------------------------------------+
```

Nun ist die Domain "foobar.cloud" für unser Projekt registriert und einsatzbereit (status: ACTIVE).
Wir wollen im nächsten Schritt MX-Records (Datensätze für Mailserver in dieser Zone) für diese Domain erstellen.

Doch zuerst schauen wir, welche Inhalte (Recordsets) unsere neue Zone bereits jetzt besitzt.

```bash
$ openstack recordset list foobar.cloud.
+--------------------------------------+---------------+------+--------------------------------------------------------------------------------+--------+--------+
| id                                   | name          | type | records                                                                        | status | action |
+--------------------------------------+---------------+------+--------------------------------------------------------------------------------+--------+--------+
| 4b666317-6170-4d71-8962-94f1cbe94dda | foobar.cloud. | NS   | ns1.openstack.de-west-01.wiit-cloud.io.                                                        | ACTIVE | NONE   |
|                                      |               |      | ns2.openstack.de-west-01.wiit-cloud.io.                                                        |        |        |
| 7915c9c1-de30-4144-b82b-36295687b0fe | foobar.cloud. | SOA  | ns2.openstack.de-west-01.wiit-cloud.io. webmaster.foobar.cloud. 1534315524 3507 600 86400 3600 | ACTIVE | NONE   |
+--------------------------------------+---------------+------+--------------------------------------------------------------------------------+--------+--------+
```

Hier sehen wir eine "leere Hülle" einer Domain mit automatisch erstellen NS und SOA-Einträgen, die sofort zur Abfrage bereit stehen.

```bash
$ dig +short @ns1.openstack.de-west-01.wiit-cloud.io foobar.cloud NS
ns1.openstack.de-west-01.wiit-cloud.io.
ns2.openstack.de-west-01.wiit-cloud.io.

$ dig +short @ns1.openstack.de-west-01.wiit-cloud.io foobar.cloud SOA
ns2.openstack.de-west-01.wiit-cloud.io. webmaster.foobar.cloud. 1534315524 3507 600 86400 3600
```

Anlegen eines MX-Records:
Nun können wir Datensätze innerhalb dieser Zone hinzufügen, verändern oder löschen (openstack recordset --help).
Als nächstes fügen wir einen MX und einen A-Record hinzu. Bei den MX-Records richten wir auch gleich die typischen Mailserver-Prioritäten (10,20) mit ein. Wobei immer der niedrigere Wert als erstes angesteuert wird und der zweite Eintrag als "Backup" dient.

```bash
$ openstack recordset create --record '10 mx1.foobar.cloud.' --record '20 mx2.foobar.cloud.' --type MX foobar.cloud. foobar.cloud.
+-------------+--------------------------------------+
| Field       | Value                                |
+-------------+--------------------------------------+
| action      | CREATE                               |
| created_at  | 2018-08-15T07:15:32.000000           |
| description | None                                 |
| id          | 4197e380-dc61-4e1d-bf92-0dcf5344eafa |
| name        | foobar.cloud.                        |
| project_id  | 2b62bc8ff48445f394d0318dbd058967     |
| records     | 10 mx1.foobar.cloud.                 |
|             | 20 mx2.foobar.cloud.                 |
| status      | PENDING                              |
| ttl         | None                                 |
| type        | MX                                   |
| updated_at  | None                                 |
| version     | 1                                    |
| zone_id     | 036ae6e6-6318-47e1-920f-be518d845fb5 |
| zone_name   | foobar.cloud.                        |
+-------------+--------------------------------------+

$ openstack recordset list foobar.cloud.
+--------------------------------------+---------------+------+--------------------------------------------------------------------------------+---------+--------+
| id                                   | name          | type | records                                                                        | status  | action |
+--------------------------------------+---------------+------+--------------------------------------------------------------------------------+---------+--------+
| 4b666317-6170-4d71-8962-94f1cbe94dda | foobar.cloud. | NS   | ns1.openstack.de-west-01.wiit-cloud.io.                                                        | ACTIVE  | NONE   |
|                                      |               |      | ns2.openstack.de-west-01.wiit-cloud.io.                                                        |         |        |
| 7915c9c1-de30-4144-b82b-36295687b0fe | foobar.cloud. | SOA  | ns2.openstack.de-west-01.wiit-cloud.io. webmaster.foobar.cloud. 1534317332 3507 600 86400 3600 | PENDING | UPDATE |
| 4197e380-dc61-4e1d-bf92-0dcf5344eafa | foobar.cloud. | MX   | 20 mx2.foobar.cloud.                                                           | PENDING | CREATE |
|                                      |               |      | 10 mx1.foobar.cloud.                                                           |         |        |
+--------------------------------------+---------------+------+--------------------------------------------------------------------------------+---------+--------+

```

```bash
$ openstack recordset create --type A --record 1.2.3.4 foobar.cloud. www
+-------------+--------------------------------------+
| Field       | Value                                |
+-------------+--------------------------------------+
| action      | CREATE                               |
| created_at  | 2018-08-15T07:28:15.000000           |
| description | None                                 |
| id          | d932688f-21d5-44b1-aa27-030c342788e7 |
| name        | www.foobar.cloud.                    |
| project_id  | 2b62bc8ff48445f394d0318dbd058967     |
| records     | 1.2.3.4                              |
| status      | PENDING                              |
| ttl         | None                                 |
| type        | A                                    |
| updated_at  | None                                 |
| version     | 1                                    |
| zone_id     | 036ae6e6-6318-47e1-920f-be518d845fb5 |
| zone_name   | foobar.cloud.                        |
+-------------+--------------------------------------+
```

Resultat:

```bash
$ openstack recordset list foobar.cloud.
+--------------------------------------+-------------------+------+--------------------------------------------------------------------------------+--------+--------+
| id                                   | name              | type | records                                                                        | status | action |
+--------------------------------------+-------------------+------+--------------------------------------------------------------------------------+--------+--------+
| 4b666317-6170-4d71-8962-94f1cbe94dda | foobar.cloud.     | NS   | ns1.openstack.de-west-01.wiit-cloud.io.                                                        | ACTIVE | NONE   |
|                                      |                   |      | ns2.openstack.de-west-01.wiit-cloud.io.                                                        |        |        |
| 7915c9c1-de30-4144-b82b-36295687b0fe | foobar.cloud.     | SOA  | ns2.openstack.de-west-01.wiit-cloud.io. webmaster.foobar.cloud. 1534318095 3507 600 86400 3600 | ACTIVE | NONE   |
| 4197e380-dc61-4e1d-bf92-0dcf5344eafa | foobar.cloud.     | MX   | 20 mx2.foobar.cloud.                                                           | ACTIVE | NONE   |
|                                      |                   |      | 10 mx1.foobar.cloud.                                                           |        |        |
| d932688f-21d5-44b1-aa27-030c342788e7 | www.foobar.cloud. | A    | 1.2.3.4                                                                        | ACTIVE | NONE   |
+--------------------------------------+-------------------+------+--------------------------------------------------------------------------------+--------+--------+
```

Wenn die Recordsets aktiv sind können wir die dafür vorgesehenen DNS-Server

* ns1.openstack.de-west-01.wiit-cloud.io
* ns2.openstack.de-west-01.wiit-cloud.io
nach diesen Records abfragen.

```bash
$ dig +short @ns1.openstack.de-west-01.wiit-cloud.io foobar.cloud NS
ns1.openstack.de-west-01.wiit-cloud.io.
ns2.openstack.de-west-01.wiit-cloud.io.

$ dig +short @ns1.openstack.de-west-01.wiit-cloud.io foobar.cloud MX
10 mx1.foobar.cloud.
20 mx2.foobar.cloud.

$ dig +short @ns1.openstack.de-west-01.wiit-cloud.io foobar.cloud www.foobar.cloud
1.2.3.4
```

> ACHTUNG! Zu diesem Zeitpunkt ist diese Domaine (foobar.cloud) noch nicht weltweit auflösbar.

Damit dieses Konstrukt weltweit benutzt werden kann, muss jede im Designate verwaltete Domain bei dem jeweiligen Registrar die Delegation zu den Nameservern `ns1.openstack.de-west-01.wiit-cloud.io` und `ns2.openstack.de-west-01.wiit-cloud.io` eingerichtet haben.

> Details zu unseren authoritativen DNS-Server:
>
> * ns1.openstack.de-west-01.wiit-cloud.io: '85.14.241.161' / '2001:4ba0:133d::53'
> * ns2.openstack.de-west-01.wiit-cloud.io: '85.14.241.162' / '2001:4ba0:133d::5353'

Um die die Mail-Records abzuschliessen, bietet sich noch an für die Mailserver entsprechende A-Records zu hinterlegen

```bash
$ openstack recordset create --type A --record 2.3.4.5 foobar.cloud. mx1
+-------------+--------------------------------------+
| Field       | Value                                |
+-------------+--------------------------------------+
| action      | CREATE                               |
| created_at  | 2018-08-15T07:42:31.000000           |
| description | None                                 |
| id          | 630d5103-7c02-4a58-83a5-97f802cf141c |
| name        | mx1.foobar.cloud.                    |
| project_id  | 2b62bc8ff48445f394d0318dbd058967     |
| records     | 2.3.4.5                              |
| status      | PENDING                              |
| ttl         | None                                 |
| type        | A                                    |
| updated_at  | None                                 |
| version     | 1                                    |
| zone_id     | 036ae6e6-6318-47e1-920f-be518d845fb5 |
| zone_name   | foobar.cloud.                        |
+-------------+--------------------------------------+
und
$ openstack recordset create --type A --record 3.4.5.6 foobar.cloud. mx2
+-------------+--------------------------------------+
| Field       | Value                                |
+-------------+--------------------------------------+
| action      | CREATE                               |
| created_at  | 2018-08-15T07:42:56.000000           |
| description | None                                 |
| id          | 2b47dbe4-70b9-4edb-ac2f-25cb8398bacb |
| name        | mx2.foobar.cloud.                    |
| project_id  | 2b62bc8ff48445f394d0318dbd058967     |
| records     | 3.4.5.6                              |
| status      | PENDING                              |
| ttl         | None                                 |
| type        | A                                    |
| updated_at  | None                                 |
| version     | 1                                    |
| zone_id     | 036ae6e6-6318-47e1-920f-be518d845fb5 |
| zone_name   | foobar.cloud.                        |
+-------------+--------------------------------------+
```

Das Resultat nach einigen Sekunden:

```bash
$ openstack recordset list foobar.cloud.
+--------------------------------------+-------------------+------+--------------------------------------------------------------------------------+--------+--------+
| id                                   | name              | type | records                                                                        | status | action |
+--------------------------------------+-------------------+------+--------------------------------------------------------------------------------+--------+--------+
| 4b666317-6170-4d71-8962-94f1cbe94dda | foobar.cloud.     | NS   | ns1.openstack.de-west-01.wiit-cloud.io.                                                        | ACTIVE | NONE   |
|                                      |                   |      | ns2.openstack.de-west-01.wiit-cloud.io.                                                        |        |        |
| 7915c9c1-de30-4144-b82b-36295687b0fe | foobar.cloud.     | SOA  | ns2.openstack.de-west-01.wiit-cloud.io. webmaster.foobar.cloud. 1534318976 3507 600 86400 3600 | ACTIVE | NONE   |
| 4197e380-dc61-4e1d-bf92-0dcf5344eafa | foobar.cloud.     | MX   | 20 mx2.foobar.cloud.                                                           | ACTIVE | NONE   |
|                                      |                   |      | 10 mx1.foobar.cloud.                                                           |        |        |
| d932688f-21d5-44b1-aa27-030c342788e7 | www.foobar.cloud. | A    | 1.2.3.4                                                                        | ACTIVE | NONE   |
| 630d5103-7c02-4a58-83a5-97f802cf141c | mx1.foobar.cloud. | A    | 2.3.4.5                                                                        | ACTIVE | NONE   |
| 2b47dbe4-70b9-4edb-ac2f-25cb8398bacb | mx2.foobar.cloud. | A    | 3.4.5.6                                                                        | ACTIVE | NONE   |
+--------------------------------------+-------------------+------+--------------------------------------------------------------------------------+--------+--------+
```

## Reverse DNS

Revers DNS Einträge für ipv4 Floating IPs können gesetzt werden sobald diese in Ihrem Projekt zugeorndet sind.
Um Reverse DNS Einträge für ipv6 Adressen zu verwalten muss zunächst eine Zone für Ihren ipv6 Prefix angelegt werden, dies können Sie über den Support der WIIT AG anfragen.
Bitte nennen Sie dazu dem Support Ihren ipv6 Prefix und Ihre Projekt ID. Nachdem die Zone für Sie erstellt wurde erhalten sie weitere Anweisungen um einen Zonentransfer in Ihr Projekt zu akzeptieren. Im Anschluss können Sie ipv6 reverse DNS Einträge innerhalb Ihrer Zone verwalten. 
