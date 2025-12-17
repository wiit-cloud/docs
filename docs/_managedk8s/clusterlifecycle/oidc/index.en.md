---
title: OIDC
lang: "en"
permalink: /managedk8s/clusterlifecycle/oidc/
nav_order: 3350
parent: Cluster Lifecycle
---
# OIDC

You can add a custom oidc configuration for the cluster.

Available variables that can be passed to the kubernetes API:
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

You don't need all variables. This is highly dependent on your oidc Provider. Please check your provider Documentation for details. 

Example for gitlab as oidc provider:
```yaml
oidc_client_id: <asdasdasdasdasdasdasdasdasd>
oidc_groups_claim: groups
oidc_groups_prefix: 'oidc:'
oidc_issuer_url: https://gitlab.address.example
oidc_signing_algs: RS256
oidc_username_claim: sub
oidc_username_prefix: https://gitlab.address.example#
```

# Requirements
To use the oidc login for kubernetes you need a kubectl plugin, a valid / prepared `kubeconfig` and [RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) permissions.

## kubectl plugin
To handle the auth part, automatically you need a plugin for kubectl. This can be found here: [int128/kubelogin](https://github.com/int128/kubelogin)

**Hint:** There is an kubectl plugin manager: [krew](https://github.com/kubernetes-sigs/krew) this could be useful if you handle more than one plugin.

## kubeconf
The kubeconf must reflect the oidc

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
You need the proper [RBAC](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) configuration / permissions. Check out the official documentation for this topic.

### Roles / ClusterRoles
You need a `Role` / `ClusterRole` to define the access of the oidc users. As an example:
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

### Rolebinding / ClusterRoleBinding
You need a `RoleBinding` / `ClusterRoleBinding` to bind the role to an oidc user. For example: 
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

