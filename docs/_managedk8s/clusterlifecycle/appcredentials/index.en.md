---
title: OpenStack Application Credentials
lang: "en"
permalink: /managedk8s/clusterlifecycle/appcredentials/
nav_order: 3300
parent: Cluster Lifecycle
---

To set up a managed Kubernetes cluster on OpenStack, we require a pair of OpenStack application credentials to ensure proper access within the OpenStack project. Follow the guidelines in the [OpenStack documentation](/openstack/quickstart/application_credentials/) to create and manage these credentials securely.

Create the application credentials as **unrestricted** (`openstack application credential create --unrestricted ...`, or "Unrestricted" in the dashboard).
The managed Kubernetes service uses them to create EC2 credentials for the etcd backups of your cluster, and OpenStack only allows this for unrestricted application credentials.
With restricted credentials your cluster runs, but gets **no etcd backups and no etcd defragmentation**.

Please be aware:
 - Do not send the credentials through unsecured channels; use only the tools and methods agreed upon during onboarding.
 - Application credentials should be associated with a dedicated service account, not a personalized user account. This is to avoid issues related to credentials being revoked when a user is offboarded.
 - If needed, we can rotate the application credentials within the cluster.
