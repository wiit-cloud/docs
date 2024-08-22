---
title: OIDC
lang: "en"
permalink: /managedk8s/clusterlifecycle/oidc/
nav_order: 3350
parent: Cluster Lifecycle
---
# OIDC

You can add a custom oidc configuration in the values.yaml for the cluster. This can only be done in the cluster creation process.

```yaml
oidc_ca_file: "path_to_file"
oidc_client_id: "12345"
oidc_groups_claim: "email"
oidc_groups_prefix: "oidc:"
oidc_issuer_url: "https://..."
oidc_required_claims:
- 'key=value'
oidc_signing_algs: "RS256"
oidc_username_claim: "sub"
oidc_username_prefix: "..."
```
