---
# We use sentence case and present imperative tone
title: Import and export a Staged Configuration
# Weights are assigned in increments of 100: determines sorting order
weight: 300
# Creates a table of contents and sidebar, useful for large documents
toc: true
# Types have a 1:1 relationship with Hugo archetypes, so you shouldn't need to change this
type: tutorial
# Intended for internal catalogue and search, case sensitive:
# Agent, N4Azure, NIC, NIM, NGF, NAP-DOS, NAP-WAF, NGINX One, NGINX+, Solutions, Unit
product:
---

## Overview

Many administrators do their work on local systems, virtual machines, Docker containers, and more. F5 NGINX One Console
supports import and export of such configurations.
This guide explains how to import or export a Staged Configuration to your NGINX One Console. 

{{< include "nginx-one/staged-config-overview.md" >}}

## Before you start

Before you import or export a Staged Configuration to NGINX One Console, ensure:

- You have an NGINX One Console account with staged configuration permissions.

## Considerations

NGINX One Console supports imports and exports as a compressed archive known as a [tarball](https://en.wikipedia.org/wiki/Tar_(computing)), in `tar.gz` format. 
When you work with such archives, consider the following:

- Do _not_ unpack archives directly to your NGINX configuration directories. You do not want to accidentally overwrite existing configuration files.
- We ignore SSL/TLS certificate files and keys
  - If you import or export such files in archives, NGINX One Console ignores such files

## Import a Staged Configuration

To import a Staged Configuration, you need to:

- Package your configuration in `tar.gz` format. For example, the following command sets up an archive from the files in the `/etc/nginx` directory:
  ```bash
  tar czvf /etc/nginx for-import.tar.gz
  ```

You would then import that file to the NGINX One Console. To do so, follow these steps:

1. On the left menu, select **Staged Configurations**.
1. Select **Import**.
1. 

## Export a Staged Configuration

You can export a Staged Configuration from the NGINX One Console. To do so, follow these stepy:

1. On the left menu, select **Staged Configurations**.
1. Select the Staged Configuration you want to export. 
1. Select the ellipsis (...) on the right side of the row with the Staged Configuration.
1. Select **Export**
1. In the file menu that appears, choose a filename for your archive and save the result
1. Avoid unpacking the archive in a way that overwrites your current NGINX configuration.
