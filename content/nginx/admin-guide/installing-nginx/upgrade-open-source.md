---
# We use sentence case and present imperative tone
title: "Upgrade from NGINX Open Source to NGINX Plus"
# Weights are assigned in increments of 100: determines sorting order
weight: 700
# Creates a table of contents and sidebar, useful for large documents
toc: false
# Types have a 1:1 relationship with Hugo archetypes, so you shouldn't need to change this
nd-content-type: how-to
# Intended for internal catalogue and search, case sensitive:
# Agent, N4Azure, NIC, NIM, NGF, NAP-DOS, NAP-WAF, NGINX One, NGINX+, Solutions, Unit
nd-product: NGINX+
---

This document describes how to upgrade NGINX Open Source to F5 NGINX Plus.

## Before you begin

To complete this guide, you will need the following prerequisites:

- An NGINX Plus subscription (purchased or trial)
- Credentials to the [MyF5 Customer Portal](https://account.f5.com/myf5), provided by email from F5, Inc.
- A [supported operating system]({{< ref "/nginx/technical-specs.md" >}})
- `root` privilege

## Back up existing data


## Identify the required NGINX Plus package

[//]: # "Link to tech specs, provide an example of how to match"

## Uninstall NGINX Open Source

[//]: # "There does not seem to be existing uninstall documentation: assumed knowledge?"


## Install NGINX Plus

[//]: # "Start by adding the NGINX Plus repository, which can differ based on operating system"
[//]: # "Could link to existing NGINX Plus documentation but redocumenting for better flow might make sense"
[//]: # "Use systemctl to check status at end"

## Move existing configuration files

[//]: # "Use nginx -t to verify configuration files at end"


## Apply configuration files


## Next steps

[//]: # "Use systemctl to check status at end"