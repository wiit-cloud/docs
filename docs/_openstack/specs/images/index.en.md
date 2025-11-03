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

- Ubuntu 24.04 LTS (Noble Numbat)
- Ubuntu Minimal 24.04 LTS (Noble Numbat)
- Ubuntu 22.04 LTS (Jammy Jellyfish)
- Ubuntu Minimal 22.04 LTS (Jammy Jellyfish)
- Debian 12 (Bookworm)
- Debian 11 (Bullseye)
- CoreOS (stable)
- Rocky Linux 9
- Flatcar Linux
- Windows Server 2022 GUI
- Windows Server 2025 GUI

These images are checked for new releases daily. The latest available version is always a public image, and contains the `Latest`-suffix. All previous versions of an imags are automatically converted to "community images", renamed (`Latest` is replaced by the date of the first upload), and eventially deleted if they are no longer in use at all.

OpenStack and many deployment tools support using these images either by name or by their UUID. By using a name, for example `Ubuntu 24.04 Noble Numbat - Latest`, you can easily stay up to date by redeploying or rebuilding your instances, even if we replace the image in the interim. You can avoid this behaviour by using the UUID instead. This may be useful for cluster deployments, where you want to ensure that all nodes are running the same version of the image.

## Linux Images

All of our provided linux images are unmodified and come directly from their official maintainers. We test them during the upload process to ensure they are deployable.

## Windows Images

We provide the following Windows images:

- Windows Server 2022 GUI
- Windows Server 2025 GUI

### Using Windows images:

Windows images support automatic password reset using instance metadata:

- Add metadata key `admin_pass` with a desired password.
- After the instance reboots, the Administrator password is set to the provided value.

{: .warning }

Remove the metadata after the initial setup. The metadata is **not encrypted** and should only be used for initial password setup.

Example:

```
openstack server set --property admin_pass='MySecurePassword123!' INSTANCE_NAME
```

- The password will be applied after the next reboot.
- Remove metadata afterwards to secure the instance:

```
openstack server unset --property admin_pass INSTANCE_NAME
```

Alternatively, you can connect via SSH and the floating IP to set the Administrator password directly inside the instance:

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

This method avoids storing the password in unencrypted metadata.

## Uploading your own images

Instead of using the images we provide, you can upload your own images. Easiest way of doing so is to use the OpenStack CLI.

```bash
openstack image create \
  --property hw_disk_bus=scsi \
  --property hw_qemu_guest_agent=True \
  --property hw_scsi_model=virtio-scsi \
  --property os_require_quiesce=True \
  --private \
  --disk-format qcow2 \
  --container-format bare \
  --file ~/my-image.qcow2 \
  my-image
```

The command to upload images requires these fields at a minimum:

- `--disk-format`: qcow2, in this case. This depends on the image format.
- `--file`: The source file on your machine
- Name of the Image: `my-image` for example.

Additionally, to enable the creation of Snapshots on running Instances, we recommend that you set `--property hw_qemu_guest_agent=True` on the images you create, and to install the `qemu-guest-agent` upon creation of the new image. See our [FAQ](https://docs.wiit-cloud.io/de/openstack/faq/#why-am-i-unable-to-create-a-snapshot-of-a-running-instance) for more details.

You can also use the dashboard to upload images. Make sure to use the same properties there.
