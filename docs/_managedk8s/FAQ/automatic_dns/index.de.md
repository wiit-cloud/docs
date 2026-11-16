---
title: Kann ich external-dns und OpenStack Designate für automatisches DNS verwenden?
lang: "de"
permalink: /managedk8s/faq/automatic_dns
nav_order: 4100
parent: FAQ
---
# Kann ich [external-dns](https://github.com/kubernetes-sigs/external-dns) und OpenStack Designate für automatisches DNS verwenden?

Ja. Mit [external-dns](https://github.com/kubernetes-sigs/external-dns) können Sie den manuellen Aufwand verringern und die Konfiguration von DNS-Zonen automatisieren.
Kurz gesagt: Mit [external-dns](https://github.com/kubernetes-sigs/external-dns) steuern Sie DNS-Records dynamisch über Kubernetes-Ressourcen, unabhängig vom DNS-Provider. [external-dns](https://github.com/kubernetes-sigs/external-dns) ist selbst kein DNS-Server. Es konfiguriert nur andere DNS-Provider (zum Beispiel OpenStack Designate, Amazon Route53, Google Cloud DNS usw.).

## Voraussetzungen {#prerequisites}

Für die folgenden Schritte benötigen Sie:

* `kubectl` in der [neuesten Version](https://kubernetes.io/docs/tasks/tools/)
* Einen laufenden Kubernetes-Cluster auf unserem OpenStack
* Eine gültige `kubeconfig` für Ihren Cluster
* Installierte [OpenStack-CLI-Tools](https://docs.openstack.org/newton/user-guide/common/cli-install-openstack-command-line-clients.html)
* [Zugriff auf die OpenStack-API](/openstack/quickstart/application_credentials/)
* Eine gültige Domain

## Domain für Designate konfigurieren {#configure-your-domain-to-use-designate}

Delegieren Sie Ihre Domains bei Ihrem DNS-Provider an die folgenden DNS-Nameserver. Dann kann Designate die DNS-Ressourcen Ihrer Domain steuern.

```bash
ns1.openstack.de-west-01.wiit-cloud.io
ns2.openstack.de-west-01.wiit-cloud.io
```

## Neue DNS-Zone erstellen {#create-a-new-dns-zone}

Bevor Sie ExternalDNS verwenden, müssen Sie Ihre DNS-Zonen bei Ihrem DNS-Provider hinzufügen. In diesem Fall ist das Designate DNS.

In unserem Beispiel verwenden wir den Test-Domainnamen `example.foo.` Erstellen Sie die Zonen unbedingt, bevor Sie die DNS-Ressourcen mit Kubernetes steuern.

> **Hinweis:**
> Sie müssen den abschließenden `.` am Ende der neuen Zone/Domain angeben.

```bash
$ openstack zone create --email webmaster@example.foo example.foo.
```

Prüfen Sie danach, ob die Zone erfolgreich erstellt wurde und der Status aktiv ist.

```bash
$ openstack zone list
$ openstack zone show example.foo.
```

## Application Credentials für [external-dns](https://github.com/kubernetes-sigs/external-dns) erstellen {#create-application-credentials-for-external-dns}

> **Achtung:**
> Melden Sie sich unbedingt mit Ihrem **Service-User** in Ihrem OpenStack-Projekt an, denn **Application Credentials** sind benutzerspezifisch.

Öffnen Sie die Web-UI und gehen Sie zu **Identity -> Application Credentials**. Klicken Sie dann auf die Schaltfläche `+ CREATE APPLICATION CREDENTIAL`. Oder
erstellen Sie die Credentials über die CLI:

```bash
$ openstack application credential create <name_of_app_credentials>
```

## [external-dns](https://github.com/kubernetes-sigs/external-dns) im Cluster installieren {#install-external-dns-into-your-cluster}

> **Hinweis:**
> Ändern Sie die OpenStack Application Credentials und die Kommandozeilenargumente `--domain-filter=example.foo` und `--txt-owner-id=<owner-id>` im Deployment von [external-dns](https://github.com/kubernetes-sigs/external-dns).
> Der Domain-Filter ist die DNS-Zone. Mit der TXT-Owner-ID erkennt external-dns die Einträge, die es verwaltet. Wenn Sie es in Produktion verwenden wollen, ändern Sie das Image auf eine neuere Version, falls verfügbar, und halten Sie es aktuell.

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

## Service oder Ingress annotieren {#annotate-the-service-or-ingress}

Um den DNS-Eintrag in Designate hinzuzufügen, annotieren Sie den Service oder Ingress mit `external-dns.alpha.kubernetes.io/hostname: my-app.example.foo`.
Beispiel:

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

## DNS-Lookup {#dns-lookup}

Prüfen Sie, ob der DNS-Record korrekt aufgelöst wird:

```sh
$ openstack recordset list example.foo.
$ dig my-app.example.foo @ns1.openstack.de-west-01.wiit-cloud.io.
$ dig my-app.example.foo @ns2.openstack.de-west-01.wiit-cloud.io.
$ dig my-app.example.foo
```

## Weitere Informationen {#further-information}

Weitere Informationen finden Sie hier:

* [kubernetes-sigs.github.io/external-dns/latest/docs/tutorials/designate](https://kubernetes-sigs.github.io/external-dns/latest/docs/tutorials/designate)
* [github.com/kubernetes-sigs/external-dns](https://github.com/kubernetes-sigs/external-dns)
* [docs.openstack.org/python-designateclient](https://docs.openstack.org/python-designateclient/latest)
