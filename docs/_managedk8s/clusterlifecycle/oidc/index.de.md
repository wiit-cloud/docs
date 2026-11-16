---
title: OIDC
lang: "de"
permalink: /managedk8s/clusterlifecycle/oidc/
nav_order: 3350
parent: Cluster Lifecycle
---
# OIDC

Sie können eine eigene OIDC-Konfiguration für den Cluster hinzufügen.

Verfügbare Variablen, die an die Kubernetes-API übergeben werden können:
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

Sie benötigen nicht alle Variablen. Das hängt stark von Ihrem OIDC-Provider ab. Details finden Sie in der Dokumentation Ihres Providers.

Beispiel für GitLab als OIDC-Provider:
```yaml
oidc_client_id: <asdasdasdasdasdasdasdasdasd>
oidc_groups_claim: groups
oidc_groups_prefix: 'oidc:'
oidc_issuer_url: https://gitlab.address.example
oidc_signing_algs: RS256
oidc_username_claim: sub
oidc_username_prefix: https://gitlab.address.example#
```

# Voraussetzungen
Für den OIDC-Login in Kubernetes benötigen Sie ein kubectl-Plugin, eine gültige und vorbereitete `kubeconfig` und [RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)-Berechtigungen.

## kubectl-Plugin
Für die automatische Authentifizierung benötigen Sie ein kubectl-Plugin: [int128/kubelogin](https://github.com/int128/kubelogin).

**Hinweis:** Es gibt einen Plugin-Manager für kubectl: [krew](https://github.com/kubernetes-sigs/krew). Er kann nützlich sein, wenn Sie mehr als ein Plugin verwenden.

## kubeconfig
Die kubeconfig muss die OIDC-Konfiguration abbilden:

```yaml
apiVersion: v1
kind: Config
preferences: {}
clusters:
- cluster:
    certificate-authority-data: ABC=
    server: https://api.cluster.example:6443
  name: example-cluster-0
contexts:
- context:
    cluster: example-cluster-0
    namespace: mi5
    user: oidc
  name: example-cluster-0
users:
- name: oidc
  user:
    exec:
      apiVersion: client.authentication.k8s.io/v1beta1
      args:
      - oidc-login
      - get-token
      - --oidc-issuer-url=https://gitlab.address.example
      - --oidc-client-id=asdasdasdasd
      - --oidc-client-secret=qweqweqweqweqweqweqwe
      command: kubectl
      env: null
      interactiveMode: IfAvailable
      provideClusterInfo: false

```

## RBAC
Sie benötigen die passende [RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)-Konfiguration bzw. die passenden Berechtigungen. Lesen Sie dazu die offizielle Dokumentation.

### Roles / ClusterRoles
Sie benötigen eine `Role` bzw. `ClusterRole`, um den Zugriff der OIDC-Benutzer zu definieren. Ein Beispiel:
```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: example-reader
rules:
  - apiGroups:
      - '*'
    resources:
      - '*'
    verbs: # we don't want to delete with normal roles
      - get
      - list
      - watch
  - nonResourceURLs:
      - '*'
    verbs:  # we don't want to delete with normal roles
      - get
```

### RoleBinding / ClusterRoleBinding
Sie benötigen ein `RoleBinding` bzw. `ClusterRoleBinding`, um die Rolle an einen OIDC-Benutzer zu binden. Zum Beispiel:
```yaml
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: example-oidc-binding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: example-reader
subjects:
  - kind: User # James Bond
    name: https://gitlab.address.example#007
```
