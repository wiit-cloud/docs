---
title: "OpenStack Client commands"
lang: "en"
permalink: /openstack/quckickstart/osclient_commands/
nav_order: 1013
parent: Quickstart
---

# Overview of OpenStack Client commands

To get more information about a specific subcommand, append the
`--help` flag to it.

To list all commands, you can use the `--help` flag:

```bash
openstack --help
```

## Server

With the command `openstack server` you can create, administrate, or delete a VM.

Here is a list of some common commands:

- `openstack server add`
    Adds parameters
    (Fixed IP, Floating IP, Security group, Volume) to a VM
- `openstack server create`
    Creates a VM
- `openstack server delete`
    Deletes a VM
- `openstack server list`
    Shows a list of all VMs
- `openstack server remove`
    Removes parameters (Fixed IP, Floating IP, Security
    group, Volume) from a VM
- `openstack server show`
    Shows all important information about the specified VM

## Security Group

Security Groups are used to allow or deny incoming and outgoing network
traffic based on IP adresses and ports for VMs.

You can also manage security groups in the OpenStackClient.

Here are some common commands:

- `openstack security group create`
    Creates a new security group.
- `openstack security group delete`
    Deletes a security group
- `openstack security group list`
    Shows a list of all security groups
- `openstack security group show`
    Shows all important information about a security group
- `openstack security group rule create`
    Adds a rule for a security group
- `openstack security group rule delete`
    Deletes a rule in a security group

## Network

To create VMs, a network is required. Here are some common network commands:

- `openstack network create`
    Creates a new network
- `openstack network list`
    Shows a list of all networks
- `openstack network show`
    Shows all important information about a network
- `openstack network delete`
    Deletes a network

## Router

For the VMs on your network to reach the internet, you need a router. Here are some common router commands.

- `openstack router create`
    Creates a new router
- `openstack router delete`
    Deletes a router
- `openstack router add port`
    Adds a port to a router
- `openstack router add subnet`
    Adds a subnet to a router

## Subnet

To use a virtual router correctly, you need a subnet that can be
administrated with `openstack subnet`. Here are some common commands:

- `openstack subnet create`
    Creates a new subnet
- `openstack subnet delete`
    Deletes a subnet
- `openstack subnet show`
    Shows all infomation about a subnet

## Port

Ports connect your VMs to your network. Here are some common commands:

- `openstack port create`
    Create a new port
- `openstack port delete`
    Deletes a port
- `openstack port show`
    Shows all infomation about a port

## Volume

Volumes are storage locations that persist across the existence of individual instances. Here are some common commands:

- `openstack volume create`
    Creates a new Volume
- `openstack volume delete`
    Deletes a volume
- `openstack volume show`
    Shows all infomation about a volume
