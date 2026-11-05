---
title: Download Glance Image
lang: "en"
permalink: /edge/openstack/download_glance_image/
has_children: false
nav_order: 3200
parent: Openstack
---

# Download a Glance image with OpenRC

This guide shows how to:

1. Download an OpenStack OpenRC file from Horizon.
2. Source or import the OpenRC credentials on your local system.
3. Download a Glance image with `openstack image save`.

## Prerequisites

- Horizon user with access to the target project.
- OpenStack CLI installed locally.
- For detailed client installation steps, see the OpenStack docs: [Install OpenStack command-line clients](https://docs.openstack.org/newton/user-guide/common/cli-install-openstack-command-line-clients.html).
- On macOS, you can install the CLI with Homebrew: `brew install openstackclient`.
- On Windows, use WSL and run the same shell commands as Linux/macOS.
- Enough local disk space for the image file.

## 1. Download the OpenRC file from Horizon

1. Log in to Horizon.
2. Open your project.
3. In the top-right user menu, click your user name.
4. Select `OpenStack RC File`.
5. Download the project OpenRC file (usually `openrc.sh`).

## 2. Configure OpenRC locally (all OS types)

### 2.1 Save the OpenRC file

Open a shell (Linux terminal, macOS Terminal, or WSL on Windows) and move the downloaded file to your working directory, for example:

```bash
mv ~/Downloads/openrc.sh ~/openstack/openrc.sh
cd ~/openstack
```

### 2.2 Source the OpenRC file

```bash
source ./openrc.sh
```

Enter your OpenStack password when prompted.

### 2.3 Verify authentication

```bash
openstack token issue
```

### 2.4 List images and copy the image ID

```bash
openstack image list
```

### 2.5 Download the image

```bash
openstack image save --file my-image.qcow2 <IMAGE_ID_OR_NAME>
```

Hint: Use `.qcow2` for `qcow2` images and `.raw` for `raw` images. You can check the image format with:

```bash
openstack image show <IMAGE_ID_OR_NAME> -f value -c disk_format
```

Example:

```bash
openstack image save --file ubuntu-22.04.qcow2 ubuntu-22.04
```

## 3. Troubleshooting

- `The resource could not be found.`
  - Check the image name/ID with `openstack image list`.
- `Missing value auth-url required for auth plugin password`
  - OpenRC variables are not loaded correctly.
- `HTTP 401 Unauthorized`
  - Re-enter password and verify project/user domain values.
