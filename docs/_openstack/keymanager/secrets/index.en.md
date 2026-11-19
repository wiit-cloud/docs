---
title: Storing Secrets
lang: "en"
permalink: /openstack/keymanager/secrets/
parent: Key Manager
nav_order: 4001
last_modified_date: 2026-11-19
---

# Storing Secrets

## Create a secret

Store a simple passphrase. The payload content type is required when storing a payload:

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

The `Secret href` is the unique reference of the secret — note it, as all further commands address secrets by href (or UUID), not by name. To capture it directly into a shell variable:

```bash
(openstack) $ SECRET_HREF=$(openstack secret store --name my-secret \
    --payload-content-type "text/plain" \
    --payload 'super-secret-value' \
    -f value -c 'Secret href')
```

Store the content of a file (for example a certificate) with `--file`:

```bash
(openstack) $ openstack secret store --name my-key \
    --payload-content-type "application/octet-stream" \
    --file my-key.bin
```

## List and read secrets

```bash
(openstack) $ openstack secret list
(openstack) $ openstack secret get <secret-href>
```

Read the payload itself:

```bash
(openstack) $ openstack secret get --payload <secret-href>
super-secret-value
```

## Delete a secret

```bash
(openstack) $ openstack secret delete <secret-href>
```

Deleting a secret is permanent. If the secret is a volume encryption key or a certificate referenced by a load balancer, the dependent resource breaks.

## Access control (ACLs)

By default only members of your project can read a secret. To grant read access to a specific user outside your project, add them to the secret's (or container's) ACL:

```bash
(openstack) $ openstack acl user add -u <user-id> <secret-or-container-href>
```

Note that no ACL grant is required for platform services like Octavia — they can already read secrets across projects in this installation. See [TLS Terminated Load Balancers](/openstack/keymanager/tls_termination/) for the certificate workflow.
