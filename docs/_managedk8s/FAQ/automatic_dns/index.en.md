---
title: Can I use external-dns and openstack designate for automatic dns?
lang: "en"
permalink: /managedk8s/faq/automatic_dns
nav_order: 4100
parent: FAQ
---
1. Login with your **service user** into your openstack project.
1. Create application credentials via WebGui **Identity -> Application Credentials**
1. Create a **gec.io** subdomain dns zone via WebGui **Project -> DNS -> Zones** for e.g. *example.gec.io*
1. Install external-dns to your cluster. Don't forget to change the openstack application credentials and the command line arguments in the external-dns deployment `--domain-filter=example.gec.io` and the `--txt-owner-id=<owner-id>`. The domain-filter is the dns zone. With the txt-owner-id external-dns can identify the entries managed by itself. You should change the image to a fixed version (and shedule updates) if you want to use it in production.

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
        OS_AUTH_URL: https://identity.optimist.innovo.cloud/v3
        OS_REGION_NAME: fra
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
                  - --domain-filter=example.gec.io
                  - --txt-owner-id=<owner-id>
                  - --log-level=debug
                envFrom:
                  - secretRef:
                      name: designate-openstack-credentials
                image: registry.k8s.io/external-dns/external-dns:latest
                imagePullPolicy: IfNotPresent
                name: external-dns

      ```

1. **Annotate** the service or ingress with `external-dns.alpha.kubernetes.io/hostname: my-app.example.gec.io` e.g.
    
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
        external-dns.alpha.kubernetes.io/hostname: my-app.example.gec.io
    spec:
      selector:
        app: nginx
      type: LoadBalancer
      ports:
        - protocol: TCP
          port: 80
          targetPort: 80
    ```

1. Make the dns record will be looked up correct:
    ```sh
    $ openstack recordset list example.gec.io.
    $ dig my-app.example.gec.io @dns1.ddns.innovo.cloud.
    ```

For setting further information, please have a look at:


* [kubernetes-sigs.github.io/external-dns/latest/docs/tutorials/designate](https://kubernetes-sigs.github.io/external-dns/latest/docs/tutorials/designate)
* [github.com/kubernetes-sigs/external-dns](https://github.com/kubernetes-sigs/external-dns)
* [docs.openstack.org/python-designateclient](https://docs.openstack.org/python-designateclient/latest)
