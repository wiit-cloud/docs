---
title: TLS-terminierte Loadbalancer
lang: "de"
permalink: /openstack/keymanager/tls_termination/
parent: Key Manager
nav_order: 4003
last_modified_date: 2026-11-19
---

# TLS-terminierte Loadbalancer

Octavia-Listener mit dem Protokoll `TERMINATED_HTTPS` terminieren TLS direkt auf dem Loadbalancer. Das Zertifikat liegt im Key Manager und wird vom Listener referenziert — private Schlüssel tauchen weder in der Loadbalancer-Konfiguration noch in API-Antworten auf. Für den allgemeinen Umgang mit Secrets siehe [Secrets hinterlegen](/openstack/keymanager/secrets/); für den Loadbalancer selbst siehe [Octavia Loadbalancer](/openstack/networking/octavia_loadbalancer/).

## 1. Zertifikat und Schlüssel hinterlegen

Zertifikat und privaten Schlüssel als separate Secrets hochladen und die zurückgegebenen hrefs merken:

```bash
(openstack) $ CERT_HREF=$(openstack secret store --name tls-cert \
    --payload-content-type "text/plain" \
    --file server.crt \
    -f value -c 'Secret href')

(openstack) $ KEY_HREF=$(openstack secret store --name tls-key \
    --payload-content-type "text/plain" \
    --file server.key \
    -f value -c 'Secret href')
```

Intermediate-Zertifikate gehören in `server.crt` (Server-Zertifikat zuerst, danach die Kette) oder in ein separates `intermediates`-Secret.

## 2. Zertifikats-Container erstellen

Die Secrets zu einem Certificate-Container gruppieren — dieser wird vom Listener referenziert:

```bash
(openstack) $ CONTAINER_HREF=$(openstack secret container create \
    --name tls-container \
    --type certificate \
    --secret "certificate=$CERT_HREF" \
    --secret "private_key=$KEY_HREF" \
    -f value -c 'Container href')
```

## 3. Listener erstellen

```bash
(openstack) $ openstack loadbalancer listener create \
    --protocol TERMINATED_HTTPS \
    --protocol-port 443 \
    --tls-container-ref $CONTAINER_HREF \
    --name https-listener \
    my-loadbalancer
```

Der Loadbalancer serviert TLS nun mit Ihrem Zertifikat; der Traffic zu den Backends kann intern plain HTTP bleiben.

## Zertifikatsrotation

Zum Rotieren neues Zertifikat und Schlüssel als neue Secrets hinterlegen, einen neuen Container erstellen und den Listener aktualisieren:

```bash
(openstack) $ openstack loadbalancer listener set \
    --tls-container-ref <new-container-href> https-listener
```

## Wissenswertes

* Das Löschen des referenzierten Containers oder seiner Secrets bricht den Listener — der Loadbalancer kann kein TLS mehr ausliefern.
* SNI für mehrere Zertifikate auf einem Listener wird über `--sni-container-refs` unterstützt.
