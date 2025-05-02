---
# We use sentence case and present imperative tone
title: "Use Content Sync Groups to Add Jwt"
# Weights are assigned in increments of 100: determines sorting order
weight: i00
# Creates a table of contents and sidebar, useful for large documents
toc: false
# Types have a 1:1 relationship with Hugo archetypes, so you shouldn't need to change this
nd-content-type: tutorial
# Intended for internal catalogue and search, case sensitive:
# Agent, N4Azure, NIC, NIM, NGF, NAP-DOS, NAP-WAF, NGINX One, NGINX+, Solutions, Unit
nd-product:
---

# Apply `license.jwt` with NGINX One Config Sync Groups (R33 Preparation)

## Overview

Starting with release R33, NGINX Plus requires a valid `license.jwt` file on each instance to operate. This guide explains how to use NGINX One Config Sync Groups (CSGs) to apply the license across multiple instances efficiently.

This method streamlines the upgrade process, reduces manual work, and provides a practical example of NGINX One's value in managing distributed configurations.

### Audience

Infrastructure and operations teams managing multiple NGINX Plus instances—particularly those using or evaluating NGINX One.

### Benefits

- Ensures compliance with licensing requirements introduced in R33 and later
- Avoids manual license distribution across servers
- Demonstrates operational value of centralized configuration management

---

## Before You Begin

### Prerequisites

- NGINX One is deployed and accessible
- You have a valid `license.jwt` file (downloaded from [MyF5](https://my.f5.com/))
- NGINX Plus instances are registered with NGINX One
- (Optional) Target instances are already assigned to a Config Sync Group

### Related Documentation

- [About Subscription Licenses](https://docs.nginx.com/solutions/about-subscription-licenses/)
- [NGINX One Overview](https://docs.nginx.com/nginx-one/)

---

## Procedure

### Step 1: Add Instances to a Config Sync Group

If your NGINX Plus instances are not yet assigned to a Config Sync Group:

1. In NGINX One, go to **Config Sync Groups**.
2. Create a new group or select an existing one.
3. Add the desired instances to the group.

If the instances are already in a Config Sync Group, proceed to the next step.

---

### Step 2: Open the Config Editor

1. Open the Config Sync Group.
2. Select **Config Editor**.
3. Review the configuration file structure for the group.

---

### Step 3: Add the `license.jwt` File

1. Navigate to the target directory (commonly `/etc/ssl/nginx`).
2. Upload the `license.jwt` file using the file editor.
3. Save the configuration.

Ensure that the file path and name align with how `nginx.conf` references the license.

---

### Step 4: Deploy the Configuration

1. Review the proposed changes.
2. Deploy the configuration to all instances in the group.
3. Confirm that the deployment completes without error.

---

### Step 5: Verify Deployment

After deployment, verify that the license has been applied:

- On the instance, run:
  ```bash
  nginx -V 2>&1 | grep JWT
  ```
- Review logs or use monitoring tools to confirm license validation.

---

## Troubleshooting

| Issue | Resolution |
|-------|------------|
| License file not found | Check the path and filename in your configuration |
| Expired JWT | Download a new `license.jwt` from MyF5 |
| Deployment fails | Check permissions, file contents, and config syntax |
| Configuration rollback needed | Use version history in the Config Editor to revert changes |

---

## Why Use This Approach

Using Config Sync Groups to distribute the license:

- Centralizes configuration across environments
- Eliminates the need to manually update each instance
- Allows consistent, version-controlled configuration management
- Supports rollback and visibility through NGINX One

---

## Next Steps

- Proceed with upgrading your NGINX Plus instances to R33 or later
- Monitor license health using NGINX One observability tools

---

### Reference (Lab-based example)

This procedure is based on internal training materials used in Lab 8 of the NGINX One SE workshop:
[GitHub: Lab 8 - Add an instance to a Config Sync Group](https://github.com/nginxinc/nginx-one-workshops/blob/SE/labs/lab8/readme.md#manually-add-an-nginx-instance-to-your-config-sync-group)
