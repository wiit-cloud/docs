---
title: Logging and Metrics
lang: "en"
permalink: /managedk8s/faq/logging_metrics/
nav_order: 4200
parent: FAQ
---
# User Guide for Logging and Metrics

This document provides an overview of logging and metrics capabilities in our Managed Kubernetes Service. Please note that we only monitor the control plane, we do not collect or store any user application data or user-generated logs from within customer-deployed workloads.
Our focus is exclusively on metrics and logs related to the cluster control plane’s health, performance, and operational efficiency.
Please note that you can access all logs/metrics endpoints even from the control plane.

# Customer Responsibility
Customers are responsible for managing their application logs, pod metrics, and data generated within their workloads. This includes:

- Collecting Application Logs: Customer may need to configure their logging solutions (e.g., Fluentd, Filebeat) to gather logs from applications and store them in a preferred location.
- Monitoring Application Metrics: For custom application metrics, customer can set up their own Prometheus or other monitoring solutions.

We recommend the following practices for managing application logs and metrics independently:

- Deploy Logging Solutions: Configure logging agents (e.g., Fluentd, Logstash) on each node to collect logs from your applications and forward them to a centralized logging service of your choice (e.g., Elasticsearch, Loki), we recommand to ship the data to our [central logging system Elastic](https://docs.wiit-cloud.io/ece/shipdata/)

- Set Up Prometheus and Grafana: For a complete metrics solution, deploy Prometheus and Grafana to monitor and visualize resource usage, custom metrics, and application performance.

He can also access all logs/metrics endpoints even from the control plane.