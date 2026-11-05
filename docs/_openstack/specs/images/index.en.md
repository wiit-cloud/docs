---
title: Images
lang: "en"
permalink: /openstack/specs/images/
parent: Specifications
nav_order: 9500
---

# Images

There are 4 types of images in OpenStack:

- **Public Images:** These images are maintained by us, available to all users, regularly updated and recommended for use.
- **Community Images:** Previously public images, which have been superseded by newer versions. We're keeping these images until they're no longer in use, so as not to compromise your deployments.
- **Private Images:** Images uploaded by you that are only available to your project.
- **Shared Images:** Private images, which are either shared by you, or with you, across multiple different projects.

Only the first two types are maintained by us.

## Public and community images

For your convenience, we're providing you with a number of selected images.

The current list of images is as follows:

- Ubuntu 26.04 LTS (Resolute Raccoon)
- Ubuntu Minimal 26.04 LTS (Resolute Raccoon)
- Ubuntu 24.04 LTS (Noble Numbat)
- Ubuntu Minimal 24.04 LTS (Noble Numbat)
- Ubuntu 22.04 LTS (Jammy Jellyfish)
- Ubuntu Minimal 22.04 LTS (Jammy Jellyfish)
- Debian 13 (Trixie)
- Debian 12 (Bookworm)
- Rocky Linux 10 (Red Quartz)
- Rocky Linux 9 (Blue Onyx)
- Flatcar Linux
- Windows Server 2022 GUI
- Windows Server 2025 GUI

These images are checked for new releases daily. The latest available version is always a public image, and contains the `Latest` suffix. All previous versions of an image are automatically converted to "community images", renamed (`Latest` is replaced by the date of the first upload), and eventually deleted if they are no longer in use at all.

OpenStack and many deployment tools support using these images either by name or by their UUID. By using a name, for example `Ubuntu 24.04 Noble Numbat - Latest`, you can easily stay up to date by redeploying or rebuilding your instances, even if we replace the image in the interim. You can avoid this behaviour by using the UUID instead. This may be useful for cluster deployments, where you want to ensure that all nodes are running the same version of the image.

## Linux Images

All of our provided Linux images are unmodified and come directly from their official maintainers. We test them during the upload process to ensure they are deployable. These images are provided for convenience purposes only, on an 'as-is' basis. Please note that we cannot provide any technical support, maintenance, or troubleshooting for them. Please upload your own custom images if you have specific requirements.

## Windows Images

We provide the following Windows images:

- Windows Server 2022 GUI
- Windows Server 2025 GUI

### Using Windows images:

{: .warning }

> Our Windows images only work with **RSA** SSH key pairs (`ssh-rsa`). If you create an instance with a different key type, such as ed25519, the initialization of the instance by cloudbase-init fails.

{: .warning }

> The initialization of our Windows images by cloudbase-init currently does not work for instances that only have an **IPv6** address. Make sure the instance also has an IPv4 address.

{: .warning }

> Windows instances run on dedicated compute nodes with proper Windows licensing. These nodes are currently only available in the availability zone [SZ1](/openstack/intro/#stretched-zone-sz1) and can only be used with [s1 flavors](/openstack/specs/flavor_specification/#s1-standard-flavors). Therefore, create Windows instances in SZ1 with an s1 flavor (e.g. `s1.large`). For SZ1, either don't specify an availability zone (SZ1 is the default) or use `--availability-zone sz1`.

Windows images support setting the initial Administrator password using instance metadata:

- Add the metadata key `admin_pass` with the desired password when creating the instance.
- The password is applied once, during the first boot of the instance. Adding or changing `admin_pass` later has no effect.

Example:

```
openstack server create \
  --image IMAGE_NAME \
  --flavor FLAVOR_NAME \
  --network NETWORK_NAME \
  --key-name KEY_NAME \
  --property admin_pass='MySecurePassword123!' \
  INSTANCE_NAME
```

{: .warning }

The metadata is **not encrypted** and remains readable after the first boot. Remove it once the instance is up:

```
openstack server unset --property admin_pass INSTANCE_NAME
```

Because the password set via `admin_pass` is transmitted as cleartext metadata, Windows requires you to change it on the first login via the GUI console (e.g. the console in the OpenStack Dashboard). Connecting via RDP is not possible until the initial password has been changed.

If you don't want to use the GUI console, you can alternatively connect via SSH and the floating IP using the public key of the key pair assigned to the instance (`--key-name`), and change the initial password directly inside the instance:

```
ssh $FLOATING_IP -l Administrator
```

{: .note }

> If you are prompted for a password when connecting via SSH, please wait a little longer. At that point, the background initialization processes of the instance may not yet be fully completed.
>
> The password you set must comply with Windows Server’s default complexity requirements. It must contain characters from at least three of the following four categories:
>
> - Uppercase letters (A–Z)
> - Lowercase letters (a–z)
> - Digits (0–9)
> - Special characters (e.g., !, $, #, %)
>
> For more information, see the [Microsoft documentation](https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh994562(v=ws.11)).

```
administrator@win-server C:\Users\Administrator> powershell
Windows PowerShell
Copyright (C) Microsoft Corporation. All rights reserved.

Install the latest PowerShell for new features and improvements! https://aka.ms/PSWindows

PS C:\Users\Administrator> $Password = Read-Host -AsSecureString
**********
PS C:\Users\Administrator> Set-LocalUser -Name Administrator -Password $Password
```

### Recommended approach for automation tools

When creating Windows instances with automation tools (e.g. Terraform or Ansible), we recommend not setting `admin_pass`. In this case, cloudbase-init generates a random password for the Administrator account during the first boot. The password is encrypted with the public key of the key pair assigned to the instance (`--key-name`) and stored in the metadata service. Only the holder of the matching private key can decrypt it.

```
openstack server create \
  --image IMAGE_NAME \
  --flavor FLAVOR_NAME \
  --network NETWORK_NAME \
  --key-name KEY_NAME \
  INSTANCE_NAME
```

As the generated password is never visible in cleartext, no password change is required: you can connect via RDP directly with the retrieved password.

The encrypted password is available a few minutes after the first boot, once cloudbase-init has finished. You can retrieve and decrypt it with the deprecated nova CLI (part of `python-novaclient`, requires `openssl` and a private key in PEM format):

```
nova get-password INSTANCE_NAME ~/.ssh/id_rsa
```

Alternatively, use the [nova-password](https://github.com/sapcc/nova-password) tool, which also supports private keys in OpenSSH and PuTTY format:

```
nova-password --private-key-path ~/.ssh/id_rsa INSTANCE_NAME
```

Both tools use the OpenStack credentials from your environment (e.g. a sourced `openrc` file).

#### Decrypting the password manually

The Compute API returns the password Base64-encoded and encrypted with RSA (PKCS #1 v1.5). Replace `COMPUTE_URL` with the public compute endpoint shown by `openstack catalog show compute`:

```
TOKEN=$(openstack token issue -f value -c id)
SERVER_ID=$(openstack server show INSTANCE_NAME -f value -c id)

curl -s -H "X-Auth-Token: $TOKEN" "COMPUTE_URL/servers/$SERVER_ID/os-server-password" \
  | jq -r .password \
  | base64 -d \
  | openssl pkeyutl -decrypt -inkey ~/.ssh/id_rsa
```

OpenSSL requires the private key in PEM format. If your key is in OpenSSH format (`-----BEGIN OPENSSH PRIVATE KEY-----`), convert a copy of it first:

```
cp ~/.ssh/id_rsa ~/.ssh/id_rsa.pem
ssh-keygen -p -m PEM -f ~/.ssh/id_rsa.pem
```

#### Automation

For proper automation, we recommend a custom script that operates directly on the Compute API:

1. Retrieve the encrypted password with `GET /servers/{server_id}/os-server-password`.
2. Decrypt it with the private key of the key pair.
3. Store it in a secrets management tool such as HashiCorp Vault.

Afterwards, you can delete the encrypted password from the metadata service with `DELETE /servers/{server_id}/os-server-password` (or `nova clear-password INSTANCE_NAME`). This only removes the stored copy; the Administrator password of the instance stays unchanged.

## Uploading your own images

Instead of using the images we provide, you can upload your own images. The easiest way of doing so is to use the OpenStack CLI.

```bash
openstack image create \
  --property hw_disk_bus=scsi \
  --property hw_qemu_guest_agent=True \
  --property hw_scsi_model=virtio-scsi \
  --property os_require_quiesce=True \
  --property hw_firmware_type=uefi \
  --property hw_machine_type=q35 \
  --private \
  --disk-format raw \
  --container-format bare \
  my-image

openstack image stage \
  --file ~/my-image.img \
  --progress \
  my-image

openstack image import \
  --method glance-direct \
  --all-stores \
  my-image
```

The command to upload images requires these fields at a minimum:

- `--disk-format`: `raw`, in this case. This depends on the image format.
- `--file`: The source file on your machine
- Name of the Image: `my-image` for example.

We recommend using the RAW format; this enables copy-on-write functionality when creating instances and volumes, significantly speeding up the process.

We also recommend using the commands mentioned above; while an image could be created in a single call, doing so would not utilize the more modern Interoperable Image API.
This API offers the ability to convert images directly upon creation and distribute them across all Availability Zones (AZs).

Additionally, to enable the creation of snapshots on running instances, we recommend that you set `--property hw_qemu_guest_agent=True` on the images you create, and to install the `qemu-guest-agent` upon creation of the new image. See our [FAQ](/openstack/faq/#why-am-i-unable-to-create-a-snapshot-of-a-running-instance) for more details.

You can also use the dashboard to upload images. Make sure to use the same properties there.

The maximum size of an image is 2 TB in the de-west-01 region. In the ch-north-01 region, the maximum size is 100 GB.
