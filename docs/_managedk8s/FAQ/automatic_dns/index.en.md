---
title: Can I use external-dns and openstack designate for automatic dns?
lang: "en"
permalink: /managedk8s/faq/automatic_dns
nav_order: 4100
parent: FAQ
---
# Can I use [external-dns](https://github.com/kubernetes-sigs/external-dns) and openstack designate for automatic dns?

Yes, to reduce manual effort and automate the configuration of DNS zones, you may want to use [external-dns](https://github.com/kubernetes-sigs/external-dns).
In summary, [external-dns](https://github.com/kubernetes-sigs/external-dns) allows you to control DNS records dynamically with Kubernetes resources in a DNS provider-agnostic way. [external-dns](https://github.com/kubernetes-sigs/external-dns) is not a DNS server by itself, but merely configures other DNS providers (for example, OpenStack Designate, Amazon Route53, Google Cloud DNS, and so on.)

## Prerequisites

To successfully complete the following steps, you need the following:

* `kubectl` [latest version](https://kubernetes.io/de/docs/tasks/tools/install-kubectl/)
* A running Kubernetes cluster on our openstack
* A valid `kubeconfig` for your cluster
* Installed [OpenStack CLI tools](https://docs.openstack.org/newton/user-guide/common/cli-install-openstack-command-line-clients.html)
* [OpenStack API access](https://docs.gec.io/optimist/guided_tour/step04/#credentials)
* A valid domain

## Configure Your domain to use designate

Delegate your domains from your DNS provider to the following DNS name servers so that Designate can control the DNS resources of your domain.

```bash
ns1.openstack.de-west-01.wiit-cloud.io
ns2.openstack.de-west-01.wiit-cloud.io
```

## Create a new DNS Zone

Before you use ExternalDNS, you need to add your DNS zones to your DNS provider, in this case, Designate DNS.

In our example we use the test domain name `example.foo.` It is important to create the zones before starting to control the DNS resources with Kubernetes.

> **Note:**
> You must include the final `.` at the end of the zone/domain to be created.

```bash
$ openstack zone create --email webmaster@example.foo example.foo.
```

Next, make sure that the zone was created successfully and the status is active.

```bash
$ openstack zone list
$ openstack zone show example.foo.
```

## Create application credentials for [external-dns](https://github.com/kubernetes-sigs/external-dns)

> **Attention:**
> It is imported to login with your **service user** into your openstack project, because **Application Credentials** are user specific.

Visit the WebGui and go to **Identity -> Application Credentials**, then click on the Button `+ CREATE APPLICATION CREDENTIAL` or
create credentials via cli:

```bash
$ openstack application credential create <name_of_app_credentials>
```

## Install [external-dns](https://github.com/kubernetes-sigs/external-dns) into your cluster

> **Note:**
> Don't forget to change the openstack application credentials and the command line arguments in the [external-dns](https://github.com/kubernetes-sigs/external-dns) deployment `--domain-filter=example.foo` and the `--txt-owner-id=<owner-id>`.
>The domain-filter is the dns zone. With the txt-owner-id external-dns can identify the entries managed by itself. You should change the image to a new version if available (and schedule updates) if you want to use it in production.

* Namespace:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: external-dns
```

* RBAC:

```yaml
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: external-dns
  namespace: external-dns
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: external-dns
rules:
  - apiGroups: [""]
    resources: ["services", "endpoints", "pods"]
    verbs: ["get", "watch", "list"]
  - apiGroups: [""]
    resources: ["pods"]
    verbs: ["get", "watch", "list"]
  - apiGroups: ["extensions", "networking.k8s.io"]
    resources: ["ingresses"]
    verbs: ["get", "watch", "list"]
  - apiGroups: [""]
    resources: ["nodes"]
    verbs: ["watch", "list"]
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: external-dns-viewer
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: external-dns
subjects:
  - kind: ServiceAccount
    name: external-dns
    namespace: external-dns
```

* Secret:

```yaml
apiVersion: v1
kind: Secret
type: Opaque
metadata:
  name: designate-openstack-credentials
  namespace: external-dns
stringData:
  OS_AUTH_URL: <auth-url>
  OS_REGION_NAME: <region>
  OS_AUTH_TYPE: v3applicationcredential
  OS_APPLICATION_CREDENTIAL_ID: <appcred_id>
  OS_APPLICATION_CREDENTIAL_SECRET: <appcred_secret>
```

* Deployment:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: external-dns
  namespace: external-dns
spec:
  selector:
    matchLabels:
      app: external-dns
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: external-dns
    spec:
      serviceAccountName: external-dns
      containers:
        - args:
            - --source=service
            - --source=ingress
            - --registry=txt
            - --provider=designate
            - --domain-filter=example.foo
            - --txt-owner-id=<owner-id>
            - --log-level=debug
          envFrom:
            - secretRef:
                name: designate-openstack-credentials
          image: registry.k8s.io/external-dns/external-dns:v0.14.2
          imagePullPolicy: IfNotPresent
          name: external-dns
```

## Annotate the service or ingress

To add the dns entry to designate the service or ingress needs to be annotated with `external-dns.alpha.kubernetes.io/hostname: my-app.example.foo`.
For example:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-app
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
  namespace: my-app
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  namespace: my-app
  annotations:
    external-dns.alpha.kubernetes.io/hostname: my-app.example.foo
spec:
  selector:
    app: nginx
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

## DNS Lookup

Make the dns record will be looked up correct:

```sh
$ openstack recordset list example.foo.
$ dig my-app.example.foo @ns1.openstack.de-west-01.wiit-cloud.io.
$ dig my-app.example.foo @ns2.openstack.de-west-01.wiit-cloud.io.
$ dig my-app.example.foo
```

## Further information

For further information, please have a look at:

* [kubernetes-sigs.github.io/external-dns/latest/docs/tutorials/designate](https://kubernetes-sigs.github.io/external-dns/latest/docs/tutorials/designate)
* [github.com/kubernetes-sigs/external-dns](https://github.com/kubernetes-sigs/external-dns)
* [docs.openstack.org/python-designateclient](https://docs.openstack.org/python-designateclient/latest)
