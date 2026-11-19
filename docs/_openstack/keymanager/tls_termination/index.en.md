---
title: TLS Terminated Load Balancers
lang: "en"
permalink: /openstack/keymanager/tls_termination/
parent: Key Manager
nav_order: 4003
last_modified_date: 2026-11-19
---

# TLS Terminated Load Balancers

Octavia listeners with the protocol `TERMINATED_HTTPS` terminate TLS directly on the load balancer. The certificate is stored in the key manager and referenced by the listener — private keys never pass through the load balancer configuration or API responses. For general secret handling see [Storing Secrets](/openstack/keymanager/secrets/); for the load balancer itself see [Octavia Load Balancers](/openstack/networking/octavia_loadbalancer/).

## 1. Store certificate and key

Upload the certificate and the private key as separate secrets and keep the returned hrefs:

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

Include intermediate certificates in `server.crt` (server cert first, chain after) or store them in a separate `intermediates` secret.

## 2. Create a certificate container

Group the secrets into a certificate container — this is what the listener references:

```bash
(openstack) $ CONTAINER_HREF=$(openstack secret container create \
    --name tls-container \
    --type certificate \
    --secret "certificate=$CERT_HREF" \
    --secret "private_key=$KEY_HREF" \
    -f value -c 'Container href')
```

## 3. Create the listener

```bash
(openstack) $ openstack loadbalancer listener create \
    --protocol TERMINATED_HTTPS \
    --protocol-port 443 \
    --tls-container-ref $CONTAINER_HREF \
    --name https-listener \
    my-loadbalancer
```

The load balancer now serves TLS with your certificate; backend traffic can stay plain HTTP on the internal network.

## Certificate rotation

To rotate, store the new certificate and key as new secrets, create a new container, then update the listener:

```bash
(openstack) $ openstack loadbalancer listener set \
    --tls-container-ref <new-container-href> https-listener
```

## Things to know

* Deleting the referenced container or its secrets breaks the listener — the load balancer can no longer serve TLS.
* SNI for multiple certificates on one listener is supported via `--sni-container-refs`.
