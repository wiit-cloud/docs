---
title: Openstack Application Credentials
lang: "en"
permalink: /managedk8s/clusterlifecycle/appcredentials/
nav_order: 3200
parent: Cluster Lifecycle
---

We need a pair of openstack application credential, so we can create the cluster in the right Openstack Project.

Look into the [Openstack Documentation](/optimist/specs/application_credentials/), how to create them.

For security reasons don't send the Credentials over unsecure ways, and only use tools that are agreed on while onboarding.

Please be aware that App Creds a bound to a user, so app creds for our tools should never be created via personalized User, as the creds will be revoked on any off boarding.
That leads to problems in the kubernetes cluster.
