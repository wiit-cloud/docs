---
title: Octavia Loadbalancers
lang: "de"
permalink: /openstack/networking/octavia_loadbalancer/
parent: Netzwerk
nav_order: 2300
---

# Der Octavia Loadbalancer

## Vorwort

[Octavia](https://wiki.openstack.org/wiki/Octavia) ist eine hochverfügbare und skalierbare Open-Source Load-Balancing-Lösung, die für die Arbeit mit OpenStack entwickelt wurde.
Octavia erledigt das Load-Balancing nach Bedarf, indem es virtuelle Maschinen – auch Amphoren genannt – in seinem Projekt verwaltet und konfiguriert.
In diesen Amphoren wirkt schlussendlich ein [HAproxy](https://www.haproxy.com/).

Alternativ steht auch der **OVN Loadbalancer Driver** zur Verfügung, welcher ohne Amphora-Instanzen arbeitet. Details hierzu finden sich im Abschnitt [Verwendung des OVN Loadbalancer Drivers](#verwendung-des-ovn-loadbalancer-drivers).

## Verwendung des OVN Loadbalancer Drivers

Neben dem klassischen amphora-Provider unterstützt unsere Plattform auch den ovn-Provider als Alternative.

Statt Amphora-VMs zu starten, nutzt der ovn-Provider die nativen Loadbalancing-Funktionen von OVN. Dadurch entfällt der Overhead durch dedizierte Amphora-Instanzen, was zu schnelleren Erstellungszeiten und geringerem Ressourcenverbrauch führt. 

{: .warning }
Der OVN-Provider unterstützt keine Layer-7-Funktionen (L7 Policies, TLS Termination, Insert Headers etc.). Er ist für einfache TCP-/UDP-Lastverteilungen auf Layer 4 gedacht.

## Der Start

Für die Nutzung von Octavia ist es notwendig, dass der Client auf dem eigenen System installiert ist. Eine Anleitung für sein System findet man [hier](/openstack/quickstart/osclient/)

## Erstellung eines Octavia-Ladbalancer

Wir geben hier die Subnet-ID an in dem der Loadbalancer erstellt werden soll.

```bash
$ openstack loadbalancer create --name Beispiel-LB --vip-subnet-id 32259126-dd37-44d5-922c-99d68ee870cd [--provider ovn]
+---------------------+--------------------------------------+
| Field               | Value                                |
+---------------------+--------------------------------------+
| admin_state_up      | True                                 |
| created_at          | 2019-05-01T09:00:00                  |
| description         |                                      |
| flavor_id           |                                      |
| id                  | e94827f0-f94d-40c7-a7fd-b91bf2676177 |
| listeners           |                                      |
| name                | Beispiel-LB                          |
| operating_status    | OFFLINE                              |
| pools               |                                      |
| project_id          | b15cde70d85749689e08106f973bb002     |
| provider            | amphora                              |
| provisioning_status | PENDING_CREATE                       |
| updated_at          | None                                 |
| vip_address         | 10.0.0.10                            |
| vip_network_id      | f2a8f00e-204b-4c37-9d19-1d5c8e4efbf6 |
| vip_port_id         | 37fc5b34-ee07-49c8-b054-a8d591a9679f |
| vip_qos_policy_id   | None                                 |
| vip_subnet_id       | 32259126-dd37-44d5-922c-99d68ee870cd |
+---------------------+--------------------------------------+
```
Standardmäßig wird der amphora-Provider verwendet.

Mit dem amphora-Provider spawnt Octavia nun die amphora-Instanzen im Hintergrund.

```bash
$ openstack loadbalancer list
+--------------------------------------+-------------+----------------------------------+--------------+---------------------+----------+
| id                                   | name        | project_id                       | vip_address  | provisioning_status | provider |
+--------------------------------------+-------------+----------------------------------+--------------+---------------------+----------+
| e94827f0-f94d-40c7-a7fd-b91bf2676177 | Beispiel-LB | b15cde70d85749689e08106f973bb002 | 10.0.0.10    | PENDING_CREATE      | amphora  |
+--------------------------------------+-------------+----------------------------------+--------------+---------------------+----------+
```

Mit dem provisioning_status `ACTIVE` ist dieser Vorgang erfolgreich abgeschlossen.

```bash
$ openstack loadbalancer list
+--------------------------------------+-------------+----------------------------------+--------------+---------------------+----------+
| id                                   | name        | project_id                       | vip_address  | provisioning_status | provider |
+--------------------------------------+-------------+----------------------------------+--------------+---------------------+----------+
| e94827f0-f94d-40c7-a7fd-b91bf2676177 | Beispiel-LB | b15cde70d85749689e08106f973bb002 | 10.0.0.10    | ACTIVE              | amphora  |
+--------------------------------------+-------------+----------------------------------+--------------+---------------------+----------+
```

## Erstellen eines LB-Listener

In unserem Beispiel wollen wir einen Listener für den HTTP-Port 80 erstellen.

Als Listener ist hier - vergleichbar mit anderen LB-Lösungen - der Port des Frontends gemeint.

```bash
$ openstack loadbalancer listener create --name Beispiel-listener --protocol HTTP --protocol-port 80 Beispiel-LB
+-----------------------------+--------------------------------------+
| Field                       | Value                                |
+-----------------------------+--------------------------------------+
| admin_state_up              | True                                 |
| connection_limit            | -1                                   |
| created_at                  | 2019-05-01T09:00:00                  |
| default_pool_id             | None                                 |
| default_tls_container_ref   | None                                 |
| description                 |                                      |
| id                          | 0a3312d1-8cf7-41a8-8d24-181246468cd7 |
| insert_headers              | None                                 |
| l7policies                  |                                      |
| loadbalancers               | e94827f0-f94d-40c7-a7fd-b91bf2676177 |
| name                        | Beispiel-listener                    |
| operating_status            | OFFLINE                              |
| project_id                  | b15cde70d85749689e08106f973bb002     |
| protocol                    | HTTP                                 |
| protocol_port               | 80                                   |
| provisioning_status         | PENDING_CREATE                       |
| sni_container_refs          | []                                   |
| timeout_client_data         | 50000                                |
| timeout_member_connect      | 5000                                 |
| timeout_member_data         | 50000                                |
| timeout_tcp_inspect         | 0                                    |
| updated_at                  | None                                 |
| client_ca_tls_container_ref |                                      |
| client_authentication       |                                      |
| client_crl_container_ref    |                                      |
+-----------------------------+--------------------------------------+
```

Der OVN-Provider unterstützt wie schon erwähnt nur L4-Protokolle, bspw. TCP / UDP.

Der Befehl war erfolgreich, wenn der `admin_state_up` `True` ist.

```bash
$ openstack loadbalancer listener list
+--------------------------------------+-----------------+-------------------+----------------------------------+----------+---------------+----------------+
| id                                   | default_pool_id | name              | project_id                       | protocol | protocol_port | admin_state_up |
+--------------------------------------+-----------------+-------------------+----------------------------------+----------+---------------+----------------+
| 0a3312d1-8cf7-41a8-8d24-181246468cd7 | None            | Beispiel-listener | b15cde70d85749689e08106f973bb002 | HTTP     |            80 | True           |
+--------------------------------------+-----------------+-------------------+----------------------------------+----------+---------------+----------------+
```

## Erstellen eines LB-Pools

Als LB-Pool ist hier eine Ansammlung aller Objekte (Listeners, Member, etc.) für zum Beispiel eine Region gemeint - vergleichbar mit einem Pool an öffentlichen IP-Adressen aus derer man sich eine belegen kann.
Einen Pool für unser Beispiel erstellt man wie folgt:

```bash
$ openstack loadbalancer pool create --name Beispiel-pool --lb-algorithm ROUND_ROBIN --listener Beispiel-listener --protocol HTTP
+----------------------+--------------------------------------+
| Field                | Value                                |
+----------------------+--------------------------------------+
| admin_state_up       | True                                 |
| created_at           | 2019-05-01T09:00:00                  |
| description          |                                      |
| healthmonitor_id     |                                      |
| id                   | 4053e88e-c2b5-47c6-987e-4387d837c88d |
| lb_algorithm         | ROUND_ROBIN                          |
| listeners            | 0a3312d1-8cf7-41a8-8d24-181246468cd7 |
| loadbalancers        | e94827f0-f94d-40c7-a7fd-b91bf2676177 |
| members              |                                      |
| name                 | Beispiel-pool                        |
| operating_status     | OFFLINE                              |
| project_id           | b15cde70d85749689e08106f973bb002     |
| protocol             | HTTP                                 |
| provisioning_status  | PENDING_CREATE                       |
| session_persistence  | None                                 |
| updated_at           | None                                 |
| tls_container_ref    |                                      |
| ca_tls_container_ref |                                      |
| crl_container_ref    |                                      |
| tls_enabled          |                                      |
+----------------------+--------------------------------------+
```

Hier sei erwähnt, dass man mit `openstack loadbalancer pool create --help` sich alle möglichen Einstellungen anzeigen lassen kann.
Die häufigsten Einstellungen und deren Auswahlmöglichkeiten:

```bash
--protocol: {TCP,HTTP,HTTPS,PROXY,PROXYV2,UDP,SCTP}
--lb-algorithm {SOURCE_IP,ROUND_ROBIN,LEAST_CONNECTIONS,SOURCE_IP_PORT}
```

Der OVN-Provider unterstützt wie schon erwähnt nur L4-Protokolle, also TCP / UDP.
Weiterhin ist für den OVN-Provider zwingend der lb-algorithm SOURCE_IP_PORT auszuwählen.

Der Pool ist erfolgreich erstellt, wenn der `provisioning_status` den Status `ACTIVE` erreicht hat.

```bash
$ openstack loadbalancer pool list
+--------------------------------------+---------------+----------------------------------+---------------------+----------+--------------+----------------+
| id                                   | name          | project_id                       | provisioning_status | protocol | lb_algorithm | admin_state_up |
+--------------------------------------+---------------+----------------------------------+---------------------+----------+--------------+----------------+
| 4053e88e-c2b5-47c6-987e-4387d837c88d | Beispiel-pool | b15cde70d85749689e08106f973bb002 | ACTIVE              | HTTP     | ROUND_ROBIN  | True           |
+--------------------------------------+---------------+----------------------------------+---------------------+----------+--------------+----------------+
```

## Erstellen der LB-`member`

Damit unser Loadbalancer weiß, an welche Backends er weiterleiten darf, fehlen uns noch sogenannte `member`, die wir wie folgt definieren:

```bash
$ openstack loadbalancer member create --subnet-id 32259126-dd37-44d5-922c-99d68ee870cd --address  10.0.0.11 --protocol-port 80 Beispiel-pool
+---------------------+--------------------------------------+
| Field               | Value                                |
+---------------------+--------------------------------------+
| address             | 10.0.0.11                            |
| admin_state_up      | True                                 |
| created_at          | 2019-05-01T09:00:00                  |
| id                  | 703e27e0-e7fe-474b-b32d-68f9a8aeef07 |
| name                |                                      |
| operating_status    | NO_MONITOR                           |
| project_id          | b15cde70d85749689e08106f973bb002     |
| protocol_port       | 80                                   |
| provisioning_status | PENDING_CREATE                       |
| subnet_id           | 32259126-dd37-44d5-922c-99d68ee870cd |
| updated_at          | None                                 |
| weight              | 1                                    |
| monitor_port        | None                                 |
| monitor_address     | None                                 |
| backup              | False                                |
+---------------------+--------------------------------------+
```

und

```bash
$ openstack loadbalancer member create --subnet-id 32259126-dd37-44d5-922c-99d68ee870cd --address  10.0.0.12 --protocol-port 80 Beispiel-pool
+---------------------+--------------------------------------+
| Field               | Value                                |
+---------------------+--------------------------------------+
| address             | 10.0.0.12                            |
| admin_state_up      | True                                 |
| created_at          | 2019-05-01T09:00:00                  |
| id                  | 2add1e17-73a6-4002-82af-538a3374e5dc |
| name                |                                      |
| operating_status    | NO_MONITOR                           |
| project_id          | b15cde70d85749689e08106f973bb002     |
| protocol_port       | 80                                   |
| provisioning_status | PENDING_CREATE                       |
| subnet_id           | 32259126-dd37-44d5-922c-99d68ee870cd |
| updated_at          | None                                 |
| weight              | 1                                    |
| monitor_port        | None                                 |
| monitor_address     | None                                 |
| backup              | False                                |
+---------------------+--------------------------------------+
```

Hier sei erwähnt, dass die beiden IP's aus 10.0.0.* bereits vorhandene, auf Port 80 lauschende Webserver sind, die eine einfache Webseite mit Info's über ihren Servicenamen ausliefern.
Unter der Vorraussetzung es handelt sich bei diesen Webservern im folgenden Beispiel um ein Ubuntu/Debian und man hat root-Berechtigungen, könnte man die einfache Webseite schnell erstellen mit:

```bash
root@BeispielInstanz1:~# apt-get update; apt-get -y install apache2; echo "you hit: you hit: webserver1" > /var/www/html/index.html


```

```bash
root@BeispielInstanz2:~# apt-get update; apt-get -y install apache2; echo "you hit: you hit: webserver2" > /var/www/html/index.html


```

Das Resultat vom Anlegen der Member können wir wie folgt überprüfen:

```bash
$ openstack loadbalancer member list Beispiel-pool
+--------------------------------------+------+----------------------------------+---------------------+--------------+---------------+------------------+--------+
| id                                   | name | project_id                       | provisioning_status | address      | protocol_port | operating_status | weight |
+--------------------------------------+------+----------------------------------+---------------------+--------------+---------------+------------------+--------+
| 703e27e0-e7fe-474b-b32d-68f9a8aeef07 |      | b15cde70d85749689e08106f973bb002 | ACTIVE              | 10.0.0.11    |            80 | NO_MONITOR       |      1 |
| 2add1e17-73a6-4002-82af-538a3374e5dc |      | b15cde70d85749689e08106f973bb002 | ACTIVE              | 10.0.0.12    |            80 | NO_MONITOR       |      1 |
+--------------------------------------+------+----------------------------------+---------------------+--------------+---------------+------------------+--------+
```

Nun ist das "interne" Konstrukt des Loadbalancers konfiguriert.

Wir haben nun:

* 2 `member` die über Port 80 den tatsächlichen Service bereitstellen und zwischen denen das Loadbalancing stattfindet,
* einen `pool` für diese `member`,
* einen `listener`, der auf Port TCP/80 lauscht und ein `ROUND_ROBIN` zu den beiden Endpunkten macht und
* einen Loadbalancer, über den wir alle Komponenten vereint haben.

Der `operating_status` `NO_MONITOR` wird unter [healthmonitor](schritt24.md#erstellen-eines-healthmonitor) korrigiert.

## Erstellen und konfigurieren der Floating-IP

Damit wir auch den Loadbalancer außerhalb unseres Beispiel-Netzwerk einsetzen können, müssen wir eine FloatingIP reservieren und diese dann mit dem `vip_port_id` des `Beispiel-LB` verknüpfen.

Mit folgendem Befehl erstellen wir uns eine Floating IP aus dem `provider`-Netz:

```bash
$ openstack floating ip create provider
+---------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field               | Value                                                                                                                                                                                                    |
+---------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| created_at          | 2019-05-01T09:00:00Z                                                                                                                                                                                     |
| description         |                                                                                                                                                                                                          |
| dns_domain          | None                                                                                                                                                                                                     |
| dns_name            | None                                                                                                                                                                                                     |
| fixed_ip_address    | None                                                                                                                                                                                                     |
| floating_ip_address | 185.116.247.133                                                                                                                                                                                          |
| floating_network_id | 54258498-a513-47da-9369-1a644e4be692                                                                                                                                                                     |
| id                  | 46c0e8cf-783d-44a0-8256-79f8ae0be7fe                                                                                                                                                                     |
| location            | Munch({'project': Munch({'domain_id': 'default', 'id': u'b15cde70d85749689e08106f973bb002', 'name': 'beispiel-tenant', 'domain_name': None}), 'cloud': '', 'region_name': 'fra', 'zone': None}) |
| name                | 185.116.247.133                                                                                                                                                                                          |
| port_details        | None                                                                                                                                                                                                     |
| port_id             | None                                                                                                                                                                                                     |
| project_id          | b15cde70d85749689e08106f973bb002                                                                                                                                                                         |
| qos_policy_id       | None                                                                                                                                                                                                     |
| revision_number     | 0                                                                                                                                                                                                        |
| router_id           | None                                                                                                                                                                                                     |
| status              | DOWN                                                                                                                                                                                                     |
| subnet_id           | None                                                                                                                                                                                                     |
| tags                | []                                                                                                                                                                                                       |
| updated_at          | 2019-05-01T09:00:00Z                                                                                                                                                                                     |
+---------------------+----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+

```

Im nächsten Schritt benötigen wir die vip_port_id des Loadbalancers. Diese bekommt man mit folgendem Befehl heraus:

```bash
$ openstack loadbalancer show Beispiel-LB -f value -c vip_port_id
37fc5b34-ee07-49c8-b054-a8d591a9679f
```

Mit dem folgendem Befehl weisen wir dem Loadbalancer nun die öffentliche IP Adresse zu. Damit ist der LB (und somit auch die Endpunkte dahinter) aus dem Internet erreichbar.

```bash
openstack floating ip set --port 37fc5b34-ee07-49c8-b054-a8d591a9679f 185.116.247.133

```

Wir sind soweit, dass wir unser Loadbalancer-Deployment testen können. Mit folgendem Befehl fragen wir unseren Loadbalancer über Port TCP/80 an und bekommen anschließend eine entsprechende Antwort von den einzelnen `member` zurück:

```bash
$ for ((i=1;i<=10;i++)); do curl http://185.116.247.133; sleep 1; done
you hit: webserver1
you hit: webserver2
you hit: webserver1
you hit: webserver2
you hit: webserver1
you hit: webserver2
you hit: webserver1
... (usw.)
```

## Erstellen eines healthmonitor

Mit dem folgenden Befehl erstellen wir einen Monitor, der bei einem Ausfall eines der Backends genau dieses fehlerhafte Backend aus der Lastverteilung nimmt und somit die Webseite oder Applikation weiterhin sauber ausgeliefert wird.

```bash
$ openstack loadbalancer healthmonitor create --delay 5 --max-retries 2 --timeout 10 --type HTTP --name Beispielmonitor --url-path / Beispiel-pool
+---------------------+--------------------------------------+
| Field               | Value                                |
+---------------------+--------------------------------------+
| project_id          | b15cde70d85749689e08106f973bb002     |
| name                | Beispielmonitor                      |
| admin_state_up      | True                                 |
| pools               | 4053e88e-c2b5-47c6-987e-4387d837c88d |
| created_at          | 2019-05-01T09:00:00                  |
| provisioning_status | PENDING_CREATE                       |
| updated_at          | None                                 |
| delay               | 5                                    |
| expected_codes      | 200                                  |
| max_retries         | 2                                    |
| http_method         | GET                                  |
| timeout             | 10                                   |
| max_retries_down    | 3                                    |
| url_path            | /                                    |
| type                | HTTP                                 |
| id                  | 368f9baa-708c-4e7f-ace1-02de15598c5d |
| operating_status    | OFFLINE                              |
| http_version        |                                      |
| domain_name         |                                      |
+---------------------+--------------------------------------+
```

Der OVN-Provider unterstützt wie schon erwähnt nur L4-Protokolle, also TCP / UDP.

In diesem Beispiel entfernt der Monitor das fehlerhafte Backend aus dem Pool, wenn die Integritätsprüfung (--type HTTP, --url-path / ) alle zwei Fünf-Sekunden-Intervalle fehlschlägt(--delay 5, --max-retries 2, --timeout 10).

Wenn der Server wiederhergestellt wird und erneut auf TCP/80 reagiert, wird er erneut zum Pool hinzugefügt.

Ein manueller Failover kann erzwungen werden, indem der Statuscodes des Webservers ungleich "200" ist, oder gar keine Antwort des Webservers erfolgt.

```bash
$ openstack loadbalancer healthmonitor show Beispielmonitor
+---------------------+--------------------------------------+
| Field               | Value                                |
+---------------------+--------------------------------------+
| project_id          | b15cde70d85749689e08106f973bb002     |
| name                | Beispielmonitor                      |
| admin_state_up      | True                                 |
| pools               | 4053e88e-c2b5-47c6-987e-4387d837c88d |
| created_at          | 2019-05-01T09:00:00                  |
| provisioning_status | ACTIVE                               |
| updated_at          | 2019-05-01T09:00:00                  |
| delay               | 5                                    |
| expected_codes      | 200                                  |
| max_retries         | 2                                    |
| http_method         | GET                                  |
| timeout             | 10                                   |
| max_retries_down    | 3                                    |
| url_path            | /                                    |
| type                | HTTP                                 |
| id                  | 368f9baa-708c-4e7f-ace1-02de15598c5d |
| operating_status    | ONLINE                               |
| http_version        |                                      |
| domain_name         |                                      |
+---------------------+--------------------------------------+
```

Aus unserem Deployment-Beispiel würde das Resultat ungefähr so aussehen:

```bash
you hit: webserver1
Mi 22 Mai 2019 17:09:39 CEST
you hit: webserver2
Mi 22 Mai 2019 17:09:40 CEST
you hit: webserver1
Mi 22 Mai 2019 17:09:41 CEST
you hit: webserver2
Mi 22 Mai 2019 17:09:42 CEST
you hit: webserver1
Mi 22 Mai 2019 17:09:43 CEST - bis hier waren beide Webserver online, doch nun ist der webserver2 offline gegangen.
you hit: webserver1
Mi 22 Mai 2019 17:09:44 CEST - noch erwarteter hit durch ROUND_ROBIN
you hit: webserver1
Mi 22 Mai 2019 17:09:50 CEST - 1. retry zu webserver2 schlägt fehl
you hit: webserver1
Mi 22 Mai 2019 17:09:56 CEST - 2. retry zu webserver2 schlägt fehl
you hit: webserver1
Mi 22 Mai 2019 17:10:01 CEST - das Backend (webserver2) wurde aus dem Pool genommen.
you hit: webserver1
Mi 22 Mai 2019 17:10:02 CEST
you hit: webserver1
Mi 22 Mai 2019 17:10:03 CEST
you hit: webserver1
Mi 22 Mai 2019 17:10:04 CEST
you hit: webserver1
Mi 22 Mai 2019 17:10:05 CEST
you hit: webserver1
Mi 22 Mai 2019 17:10:06 CEST
```

## Monitoring mit Prometheus

Der Octavia Amphora-Driver bietet einen Prometheus-Endpunkt. Auf diese Weise können Sie Metriken von Octavia-Loadbalancern sammeln.

Um einen Prometheus-Endpunkt zu einem vorhandenen Octavia-Load-Balancer hinzuzufügen, erstellen Sie einen Listener mit dem Protokoll `PROMETHEUS`. Dadurch wird der Endpunkt als `/metrics` auf dem Listener aktiviert. Der Listener unterstützt alle Funktionen eines Octavia-Load-Balancers, z. B. allowed_cidrs, unterstützt jedoch nicht das Anhängen von Pools oder L7-Richtlinien. Alle Metriken werden durch die Octavia-Objekt-ID (UUID) der Ressourcen identifiziert.

Der OVN-Provider unterstützt den Prometheus-Endpunkt nicht.

Hinweis: Derzeit werden UDP- und SCTP-Metriken nicht über Prometheus-Endpunkte gemeldet, wenn der Amphora-Provider verwendet wird.

Um beispielsweise einen Prometheus-Endpunkt auf Port 8088 für den Load Balancer `lb1` zu erstellen, führen Sie den folgenden Befehl aus:

```bash
$ openstack loadbalancer listener create --name stats-listener --protocol PROMETHEUS --protocol-port 8088 lb1
+-----------------------------+--------------------------------------+
| Field                       | Value                                |
+-----------------------------+--------------------------------------+
| admin_state_up              | True                                 |
| connection_limit            | -1                                   |
| created_at                  | 2021-10-03T01:44:25                  |
| default_pool_id             | None                                 |
| default_tls_container_ref   | None                                 |
| description                 |                                      |
| id                          | fb57d764-470a-4b6b-8820-627452f55b96 |
| insert_headers              | None                                 |
| l7policies                  |                                      |
| loadbalancers               | b081ed89-f6f8-48cb-a498-5e12705e2cf9 |
| name                        | stats-listener                       |
| operating_status            | OFFLINE                              |
| project_id                  | 4c1caeee063747f8878f007d1a323b2f     |
| protocol                    | PROMETHEUS                           |
| protocol_port               | 8088                                 |
| provisioning_status         | PENDING_CREATE                       |
| sni_container_refs          | []                                   |
| timeout_client_data         | 50000                                |
| timeout_member_connect      | 5000                                 |
| timeout_member_data         | 50000                                |
| timeout_tcp_inspect         | 0                                    |
| updated_at                  | None                                 |
| client_ca_tls_container_ref | None                                 |
| client_authentication       | NONE                                 |
| client_crl_container_ref    | None                                 |
| allowed_cidrs               | None                                 |
| tls_ciphers                 | None                                 |
| tls_versions                | None                                 |
| alpn_protocols              | None                                 |
| tags                        |                                      |
+-----------------------------+--------------------------------------+
```

Sobald der PROMETHEUS-Listener AKTIV ist, können Sie Prometheus so konfigurieren, dass es Metriken vom Load Balancer sammelt, indem Sie die Datei prometheus.yml aktualisieren.

```yaml
[scrape_configs]
- job_name: 'Octavia LB1'
  static_configs:
  - targets: ['192.0.2.10:8088']
```
