---
title: Floating IPs
lang: "en"
permalink: /openstack/networking/floating_ips/
parent: Networking
nav_order: 2100
---

Floating IPs
============

Floating IPs are public IP addresses allocated from an external network. They can be associated with a fixed IP address of a Neutron port to make it reachable from outside the cloud.

Managing floating IPs
---------------------

A floating IP is allocated from an external network:

```bash
$ openstack floating ip create <EXTERNAL_NETWORK>
```

It is then associated with a port:

```bash
$ openstack floating ip set --port <PORT> <FLOATING_IP>
```

The association can be removed again:

```bash
$ openstack floating ip unset --port <FLOATING_IP>
```

{: .warning }
> Only one floating IP can be associated with a port. While Neutron does allow multiple fixed IPs on a port and lets you associate a floating IP with each fixed IP, only the first floating IP / fixed IP combination is actually reachable — any further associations have no effect.
>
> If multiple IP addresses need to be reachable via floating IPs, separate ports have to be used. Alternatively, multiple services can be exposed on a single floating IP using port forwarding (see below).

Port Forwarding on Floating IPs
-------------------------------

Floating IP port forwarding allows users to forward traffic from a TCP/UDP/other protocol port of a floating IP to a TCP/UDP/other protocol port associated with one of the fixed IPs of a Neutron port.

### Create a port forwarding rule on a floating IP

In order to apply port forwarding on a floating IP, the following information is required:

* The internal IP address to be used
* The UUID of the port to be associated with the floating IP
* The port number of the network port's fixed IPv4 address
* The external port number of the port forwarding's floating IP address
* The specific protocol to be used in the port forwarding (in this example TCP)
* The floating IP for the port forwarding rule to be applied on.

The example below demonstrates the creation of port forwarding on a floating IP, using the required options:

```bash
$ openstack floating ip port forwarding create \
     --internal-ip-address 10.0.0.14 \
     --port 12c29300-0f8a-4c54-a9dc-bee4c12c6ad2 \
     --internal-protocol-port 80 \
     --external-protocol-port 8080 \
     --protocol tcp 185.116.244.141
```

### List port forwarding settings applied to floating IPs

Within a project, a list of port forwarding rules applied to specific floating IPs can be obtained with the following command.

`$ openstack floating ip port forwarding list 185.116.244.141`

The command above can be further refined using the `--sort-column`, `--port`, `--external-protocol-port` and `--protocol` flags before the floating IP.

### Display details of a specific port forwarding rule

To display the specific details of a port forwarding rule for a floating IP, the following command can be used:

`$ openstack floating ip port forwarding show <floating-ip> <port-forwarding-id>`

### Modify floating IP port forwarding properties

If a port forwarding configuration on a floating IP has already been created using `$ openstack floating ip port forwarding create`, further changes can be made to the existing configuration using `$ openstack floating ip port forwarding set ...`.

The following aspects of the port forwarding can be modified:

* `--port`: The UUID of the network port
* `--internal-ip-address`: The fixed internal address associated with the floating IP port forwarding rule.
* `--internal-protocol-port`: The TCP/UDP/etc. port number of the network port fixed IPv4 address associated with the floating IP port forwarding rule
* `--external-protocol-port`: The TCP/UDP/etc. port number of the port forwarding rule's floating IP address
* `--protocol`: The IP protocol used in the floating IP port forwarding rule (TCP/UDP/other)
* `--description`: Text describing/contextualizing the use of the port forwarding configuration

The configuration of any of the above options can be modified with a variation of the following command:

```bash
$ openstack floating ip port forwarding set \
     --port <port> \
     --internal-ip-address <internal-ip-address> \
     --internal-protocol-port <port-number> \
     --external-protocol-port <port-number> \
     --protocol <protocol> \
     --description <description> \
    <floating-ip> <port-forwarding-id>
```

### Delete port forwarding from a floating IP

To remove a port forwarding rule from a floating IP, we need the following information:

* The floating IP from which the port forwarding rule is to be removed.
* The port forwarding ID (this ID is assigned upon creation and can be obtained using the `$ openstack floating ip port forwarding list ...` command)

The following command removes the port forwarding rule from a floating IP:

`$ openstack floating ip port forwarding delete <floating-ip> <port-forwarding-id>`
