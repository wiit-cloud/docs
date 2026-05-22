---
title: Openstack Application Credentials
lang: "en"
permalink: /managedk8s/clusterlifecycle/appcredentials/
nav_order: 3300
parent: Cluster Lifecycle
---

To set up a managed Kubernetes cluster on OpenStack, we require a pair of OpenStack application credentials to ensure proper access within the OpenStack project. Follow the guidelines [Openstack Documentation](openstack/quickstart/application_credentials/) to create and manage these credentials securely.


Please be aware :
 - Do not send the credentials through unsecured channels, use only the tools and methods agreed upon during onboarding.
 - Application credentials should be associated with a dedicated service account, not a personalized user account. This is to avoid issues related to credentials being revoked when a user is offboarded.
 - If needed, we can rotate the application credentials within the cluster.
