---
title: Secrets hinterlegen
lang: "de"
permalink: /openstack/keymanager/secrets/
parent: Key Manager
nav_order: 4001
last_modified_date: 2026-11-19
---

# Secrets hinterlegen

## Secret erstellen

Eine einfache Passphrase hinterlegen. Der Payload-Content-Type ist beim Hinterlegen eines Payloads erforderlich:

```bash
(openstack) $ openstack secret store --name my-secret \
    --payload-content-type "text/plain" \
    --payload 'super-secret-value'
+---------------+--------------------------------------------------------------+
| Field         | Value                                                        |
+---------------+--------------------------------------------------------------+
| Secret href   | https://key-manager....../v1/secrets/5c4c...                 |
| Name          | my-secret                                                    |
| ...           |                                                              |
+---------------+--------------------------------------------------------------+
```

Der `Secret href` ist die eindeutige Referenz des Secrets — merken Sie ihn sich, denn alle weiteren Kommandos adressieren Secrets über href (oder UUID), nicht über den Namen. Um ihn direkt in eine Shell-Variable zu übernehmen:

```bash
(openstack) $ SECRET_HREF=$(openstack secret store --name my-secret \
    --payload-content-type "text/plain" \
    --payload 'super-secret-value' \
    -f value -c 'Secret href')
```

Den Inhalt einer Datei (zum Beispiel ein Zertifikat) hinterlegen Sie mit `--file`:

```bash
(openstack) $ openstack secret store --name my-key \
    --payload-content-type "application/octet-stream" \
    --file my-key.bin
```

## Secrets auflisten und lesen

```bash
(openstack) $ openstack secret list
(openstack) $ openstack secret get <secret-href>
```

Das Payload selbst auslesen:

```bash
(openstack) $ openstack secret get --payload <secret-href>
super-secret-value
```

## Secret löschen

```bash
(openstack) $ openstack secret delete <secret-href>
```

Das Löschen ist endgültig. Ist das Secret ein Volume-Verschlüsselungsschlüssel oder ein von einem Loadbalancer referenziertes Zertifikat, bricht die abhängige Ressource.

## Zugriffskontrolle (ACLs)

Standardmäßig können nur Mitglieder Ihres Projekts ein Secret lesen. Um einem bestimmten User außerhalb Ihres Projekts Lesezugriff zu geben, fügen Sie ihn der ACL des Secrets (oder Containers) hinzu:

```bash
(openstack) $ openstack acl user add -u <user-id> <secret-or-container-href>
```

Für Plattformdienste wie Octavia ist keine ACL-Freigabe nötig — diese können in dieser Installation bereits projektübergreifend Secrets lesen. Den Zertifikats-Workflow beschreibt [TLS-terminierte Loadbalancer](/openstack/keymanager/tls_termination/).
