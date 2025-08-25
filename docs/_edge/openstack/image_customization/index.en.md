---
title: Image Customization
lang: "en"
permalink: /edge/openstack/image_customization/
has_children: false
nav_order: 3130
parent: Openstack
---

# Image Customization

Prebuilt images play an important role in OpenStack to allow seamless provisioning of instances.

You can use below steps to customize or build new images.

## Table of Contents

- [Image Customization](#image-customization)
  - [Table of Contents](#table-of-contents)
  - [Customize on Boot (Cloudinit)](#customize-on-boot-cloudinit)
  - [Create images manually](#create-images-manually)
  - [Automated Image Build](#automated-image-build)

## Customize on Boot (Cloudinit)

OpenStack Instances can be injected with a custom cloud-init template which will provision resources on top of the base images on first boot.

During instance creation the cloud-init template needs to be passed via the `user_data` property. In Horizon OpenStack Dashboard there is a textbox to pass a custom cloud-init template in the Instance Creation Wizard.

The cloud-init template of OperationsCenter VMs cannot be adjusted.

## Create images manually

In cases where more customization is needed or the OS is not available as a prebuilt template a custom image can be built and uploaded to the edge by every Openstack user.

Openstack has a great documentation on how to [create images manually](https://docs.openstack.org/image-guide/create-images-manually.html).

After successfully building the image you can upload it to OpenStack with the following command:

```bash
openstack image create \
  --property hw_disk_bus=scsi \
  --property hw_scsi_model=virtio-scsi \
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

Images uploaded with `--private` can only be used in the project. Upload images with `--public` flag, if you want to allow every openstack user and OperationsCenter users to use this image for servers.

Additionally, to enable the creation of Snapshots on running Instances, we recommend that you set `--property hw_qemu_guest_agent=True` and `--property os_require_quiesce=True` on the images you create. This requires a running `qemu-guest-agent` for a successfull snapshot.

`hw_firmware_type=uefi`: This property specifies that the image should use UEFI firmware instead of legacy BIOS. UEFI provides enhanced security features and supports modern hardware configurations.

You can also use the dashboard to upload images. Make sure to use the same properties there.

## Automated Image Build

For more advanced use cases image can be built automatically. There are a variety of tools such as Packer, image-bootstrap or windows-openstack-imaging-tools.

Please see [Tools to automate image creation](https://docs.openstack.org/image-guide/create-images-automatically.html) for more information.
