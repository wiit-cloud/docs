---
title: Key Manager
lang: "en"
permalink: /openstack/keymanager/
has_children: true
nav_order: 4000
---

# Key Manager

The key manager service (Barbican) provides secure storage for secrets such as passwords, private keys and TLS certificates. Secrets are stored encrypted and are scoped to your project — other projects cannot read them.

Typical use cases:

* [Encrypted volumes](/openstack/keymanager/encrypted_volumes/) — encryption keys for encrypted Cinder volume types are generated and stored in the key manager automatically
* [TLS terminated load balancers](/openstack/keymanager/tls_termination/) — upload your certificate once and reference it on Octavia listeners
* [Generic secrets](/openstack/keymanager/secrets/) — store passphrases or keys and retrieve them via the API from your applications

## Access

Secrets can be managed with the OpenStack CLI and via the dashboards:

```bash
(openstack) $ pip install python-barbicanclient
(openstack) $ openstack secret list
```

The same operations are available in Horizon and Skyline under **Key Manager**.

## Limitations

* Secrets cannot be modified after creation — update means delete and re-create.
* Orders (server-side generation of asymmetric keys and certificates) are not supported.
* Deleting a secret is permanent. For volume encryption keys this means the data on the affected volumes is unrecoverable.
