---
title: FAQ
lang: "en"
permalink: /openstack/faq/
nav_order: 10001
last_modified_date: 2025-07-21
---

# FAQ

## The command `openstack --help` shows the error "Could not load EntryPoint.parse"

In this case some components of the OpenstackClient are outdated. To get an overview of the components that need
to be updated, use the following command:

```bash
openstack --debug --help
```

To update the components, use the command below. (Replace `<PROJECT>` with the correct
project):

```bash
pip install python-<PROJECT>client -U
```

## How can I use [VRRP](https://en.wikipedia.org/wiki/Virtual_Router_Redundancy_Protocol)?

To use VRRP, it must first be enabled in a security group which is then assigned to an actual VM. You can only add this with the
OpenStack client. For example:

```bash
openstack security group rule create --remote-ip 10.0.0.0/24 --protocol vrrp --ethertype IPv4 --ingress  default
```

To reserve the VIP in Neutron, a virtual port is created for the VIP:

```bash
openstack port create --network <NETWORK_ID> --fixed-ip subnet=<SUBNET_ID>,ip-address=<VIP_IP_CIDR> VIP_PORT
```

In addition to the security group rule to allow VRRP you need to configure the VIP as an allowed address on your instance ports:

```bash
openstack port set PORT_UUID --allowed-address ip-address=<VIP_IP_CIDR>,mac_address=<MAC_ADDRESS>
```

## Why am I charged for Floating IPs I am not using?

We have to charge for reserved Floating IPs. In this case, there is a high probability that Floating IPs were created but not deleted correctly after use.

To get an overview of your Floating IPs, you can use the [Horizon Dashboard](https://openstack.wiit-cloud.io/), where you can find the list
_Project_ → _Network_ → _Floating-IPs_.

You can accomplish the same using the OpenStack client:

```bash
$ openstack floating ip list
+--------------------------------------+---------------------+------------------+--------------------------------------+--------------------------------------+----------------------------------+
| ID                                   | Floating IP Address | Fixed IP Address | Port                                 | Floating Network                     | Project                          |
+--------------------------------------+---------------------+------------------+--------------------------------------+--------------------------------------+----------------------------------+
| 84eca713-9ac1-42c3-baf6-860ba920a23c | 185.116.245.222     | 192.0.2.7        | a3097883-21cc-49fa-a060-bccc1678ece7 | 54258498-a513-47da-9369-1a644e4be692 | b15cde70d85749689e6568f973bb002  |
+--------------------------------------+---------------------+------------------+--------------------------------------+--------------------------------------+----------------------------------+
```

## How can I change the flavor of a virtual machine (instance resize)?

In the openstack Stack you can change the flavor of an instance -- the available RAM
and vCPUs -- with the following steps:

### Resizing using the command line

With the name or UUID of the server you wish to resize, you can resize it with the openstack server resize command.

Specify the desired new flavor and then the instance name or UUID:

```bash
openstack server resize --flavor FLAVOR SERVER
```

Resizing can take some time. During this time, the instance status is displayed as RESIZE.

When the resize is complete, the instance status is displayed as VERIFY_RESIZE. To change the status to ACTIVE, confirm the resize:

```bash
openstack server resize --confirm SERVER
```

{: .warning }

Resizes are confirmed automatically after 1 hour, if not confirmed or reverted manually.

### Resizing with the openstack dashboard

On [openstack Dashboard → Instances](https://openstack.wiit-cloud.io/project/instances/) navigate to the instance to resize, then
choose: _Actions_ → _Resize Flavor_.

The current flavor is shown. To choose the new flavor, use the "Select a new flavor" dropdown list, and confirm with "Resize".

## Why are the logs of the compute instance in the openstack dashboard empty?

Due to maintenance work or load redistribution in OpenStack, the instance may have been migrated. In this case, the log file will be recreated and new messages logged here.

## Why do I get the error "Conflict (HTTP 409)" when creating a swift / S3 bucket?

Swift / S3 uses unique names across the entire OpenStack environment. The error message states that the selected name is already in use.

## HowTo Mount Cinder Volumes to Instances by UUID

When attaching multiple Cinder Volumes to an instance, the mount points may be shuffled on every reboot. Mounting the
volumes by UUID ensures that the correct volumes are reattached to the correct mount points in the event the instance requires a power cycle.

Change the mountpoint in `/etc/fstab` to use the UUID after fetching the infos with `blkid` on e.g.:

```bash
# /boot was on /dev/sda2 during installation
/dev/disk/by-uuid/f6a0d6f3-b66c-bbe3-47ba-d264464cb5a2 /boot ext4    defaults        0       2
```

## Is it possible to have multiattached volumes on Cinder?

We do not support multiattached volumes on our instances as cluster-capable file systems are required for multi attach volumes to handle concurrent file system access.
Attempts to use multi-attached volumes without cluster-capable file systems carry a high risk of data corruption, therefore this feature is not enabled on the OpenStack Platform.

## Why am I unable to create a snapshot of a running instance?

In order to provide consistent snapshots, the OpenStack Platform utilises the property [os_require_quiesce=yes](https://opendev.org/openstack/nova/commit/926e58a179ef373646164bea40dc46b1ebef4748).
This property allows `fsfreeze` to suspend and resume access on running instances and ensures ensures that a consistent image is created from the disk.

The OpenStack Platform supports the following options to facilitate the creation of snapshots on running instances:

The first option is to take a snapshot of the running instance by installing and running the `qemu-guest-agent`. It can be installed and run as follows:

```bash
apt install qemu-guest-agent
systemctl start qemu-guest-agent
systemctl enable qemu-guest-agent
```

Once the qemu-guest-agent is running, the snapshot can be created.

Additionally, when [uploading your own images](https://docs.wiit-cloud.io/openstack/specs/images/#uploading-your-own-images), we recommend that you include `--property hw_qemu_guest_agent=True` to install this upon creation of the new image.

The second option is to stop the running instance, create the snapshot, then start the instance again. This can be done via the Horizon Dashboard or on the CLI as follows:

```bash
openstack server stop ExampleInstance
openstack server image create --name ExampleInstanceSnapshot ExampleInstance
openstack server start ExampleInstance
```
