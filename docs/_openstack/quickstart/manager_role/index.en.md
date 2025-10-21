---
title: "User, Project & Group Management (Manager Role)"
lang: "en"
permalink: /openstack/quickstart/manager_role/
nav_order: 1010
parent: Quickstart
---

# Project, User & Role Management

## Dedicated OpenStack Domain & Identity Self-Service

As a Customer you have a dedicated OpenStack domain, including one or more domain manager accounts, enabling autonomous management of projects, users, groups and role assignments via the OpenStack CLI.
Domain manager users only have the permissions for the identity self-service and should not be assigned to projects within a domain.
The domains are isolated in OpenStack with a dedicated namespace for projects, groups and users.
User 2FA (Two-Factor Authentication) setup and enforcement can also be configured by the domain manager.

The following Keystone security compliance settings are applied:
  - **Password change on first login:** Can be bypassed with `--ignore-change-password-upon-first-use` at user creation
  - **Password policy:** Minimum 18 characters, must include uppercase, lowercase, number, and special character

Default quotas are applied to newly created projects, quota adjustments of individual projects can be requested via [WIIT Support](mailto:helpdesk.de@wiit.one).
Domains and domain manager users are managed by WIIT AG, changes can also be requested via [WIIT Support](mailto:helpdesk.de@wiit.one).

{: .warning }
The dashboard is not available to Manager users. While they can log in, all operations are available exclusively via CLI or API, as described below.
Application credentials cannot be generated for users without a project assignment (such as the predefined manager user).
Application credentials can only be used for operations within projects, not for domain-level operations, such as creating projects.

## Domain Manager CLI Setup

Use the following CLI options to authenticate with your domain manager user:
```bash
openstack --os-username $MANAGER_USERNAME \
          --os-password $PASSWORD \
          --os-auth-url https://identity.openstack.de-west-01.wiit-cloud.io/v3 \
          --os-user-domain-name $DOMAIN_NAME \
          --os-domain-name $DOMAIN_NAME \
          --os-auth-type password \
$COMMAND
```

As a domain manager you are able to manage users, projects, groups and role assignments with the following commands combined with the CLI objections above: 

### Obtaining the Domain ID
```bash
# Obtain the Domain ID
domain show $DOMAIN_NAME
```

{: .warning }
Use the domain ID for the commands below, not the domain name.
If the domain name consists of numbers, the OpenStack CLI may mistakenly interpret it as a domain ID, and the request will fail.

### User Management

```bash
# Create a user
user create --domain $DOMAIN_ID --password-prompt $USER

# List all users in your domain
user list

# Show user details
user show $USER

# Delete a user
user delete $USER

# Enable 2FA for a user
user set --enable-multi-factor-auth $USER

# Enforce 2FA rules (e.g., password + TOTP, or App Credential)
user set \
  --enable-multi-factor-auth \
  --multi-factor-auth-rule password,totp \
  --multi-factor-auth-rule application_credential \
  $USER

# Create TOTP secret for a user (do this as user before the multi-factor-auth-rule is applied from the manager)
openstack credential create --type totp $USER_ID $BASE_32_2FA_SECRET
```

### Project Management
```bash
# Create a new project
project create $PROJECT --domain $DOMAIN_ID

# List all projects
project list

# Delete a project
project delete $PROJECT
```

**Important:** Cleanup all resources within the project befor you deleted it, otherwise resources are accounted for until our automated cleanup jobs are executed

### Group Management
```bash
# Create a group
group create $GROUP --domain $DOMAIN_ID

# List groups
group list

# Delete a group
group delete $GROUP

# Add user to group
group add user $GROUP $USER

# Remove user from group
group remove user $GROUP $USER
```

### Role Assignmments

**Important:** Only assign the "member" Role - the "reader" role is available in the system but the effective permissions are not read-only when using the "reader" role.

```bash
# Assign role to a user in a project
role add --user $USER --project $PROJECT member

# Assign role to a group in a project
role add --group $GROUP --project $PROJECT member

# Remove role for a user
role remove --user $USER --project $PROJECT member

# Remove role for a group
role remove --group $GROUP --project $PROJECT member

# List role assignments for a user
role assignment list --names --user $USER

# List role assignments for a project
role assignment list --names --project $PROJECT

# List role assignments for a group
role assignment list --names --group $GROUP
```
