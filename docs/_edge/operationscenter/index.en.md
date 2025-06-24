---
title: Operations Center
lang: "en"
permalink: /edge/operationscenter/
has_children: false
nav_order: 2000
---

# Operations Center Releases

{: .important-title }
> End of Life announcement
>
> Operations Center development and support will end in 2026. It is planned to combine the power of the edge with our WIIT Cloud Native Platform.

## 1.5.14

#### Improvements:
- VPNaaS: Improved monitoring of Server and Gateway VMs

## 1.5.13

#### Fixes:
- limit project names to 25 chars (previously: 50)

## 1.5.11

#### Fixes:
- Backups are no longer blocked because of old VM with whitespace as the start of the name
- Limiting of the Projectname is now working as intended

## 1.5.9

#### Improvements:
- Updates to the backend Ingress

## 1.5.6

### Changes Resource Overview:

#### Fixes:
- Does not truncate ports of VMs in the tooltip anymore. Shows all ports in the tooltip now

#### Features:
- Shows internal ip additionally to floating ip

## 1.5.4

#### Features:
- Flavors can now be set to not be visible in Operations Center again.
To set a flavor to invisible update the flavor metadata in openstack and add the custom visibility, set it to false and save the change.
The flavor is now not visible in Operations Center any more.
Note: The flavor is just hidden from the selection when creating a new VM. VMs using that flavor already are not impacted and can still be managed in Operations Center

## 1.5.3

#### Fixes:
- VPNaaS revoked certs were not correctly appended to the CRL
#### Features:
- Added password generator and info tooltip for password
#### Improvements:
- External user creation blocked for emails ending with fraunhofer.de and added tooltip for info about that
- VPNaas certificates validity increased to 10 years
- Limit project name length to prevent VPNaaS issues
- Updated VPNaaS Version to support newer OpenVPN clients
- Pressing the refresh button on the resource Overview displays the IP and status information
- Improved Backup performance
