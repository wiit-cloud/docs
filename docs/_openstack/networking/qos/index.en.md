---
title: QoS
lang: "en"
permalink: /openstack/networking/qos/
nav_order: 2500
parent: Networking
---

# Network Quality of Service (QoS)

To maintain the stability of our platform and provide all customers with reliable services we implement Quality of Service (QoS) in our network.

This means that each port on our infrastructure is limited to a specific maximum bandwidth.
The limit is applied on a port level and therefore is the same for all flavor types.

The current limits are:

- Maximum ingress / egress limit for **internal ports**: 10 Gbit/s
- Maximum ingress / egress limit for **ports on the provider network**: 5 Gbit/s

Once network traffic exceeds the bandwidth limit, excess packets are dropped; for TCP connections, this typically manifests as retransmissions and increased latency.
This is the same behavior as if a physical NIC is saturated.
