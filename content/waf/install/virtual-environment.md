---
# We use sentence case and present imperative tone
title: "Deploy F5 WAF for NGINX in a virtual environment"
# Weights are assigned in increments of 100: determines sorting order
weight: 100
# Creates a table of contents and sidebar, useful for large documents
toc: true
# Types have a 1:1 relationship with Hugo archetypes, so you shouldn't need to change this
nd-content-type: how-to
# Intended for internal catalogue and search, case sensitive:
# Agent, N4Azure, NIC, NIM, NGF, NAP-DOS, NAP-WAF, NGINX One, NGINX+, Solutions, Unit
nd-product: NAP-WAF
---

{{< call-out "warning" "Information architecture note" >}}

The design intention for this page is as a standalone page for the operating system specific installation use cases:

- [v4]({{< ref "/nap-waf/v4/admin-guide/install.md#prerequisites" >}})
- [v5]({{< ref "/nap-waf/v5/admin-guide/install.md#common-steps-for-nginx-open-source-and-nginx-plus" >}})

Instead of having separate top level folders, differences between v4 and v5 will be denoted with whole page sections, tabs, or other unique signifiers.  

{{< /call-out >}}

This page describes how to install F5 WAF for NGINX on a virtual machine or bare metal environment. To review supported operating systems, please read the [Technical specifications]({{< ref "/waf/fundamentals/technical-specifications.md" >}}) guide.

There are multiple alternative deployment methods available:

- [Deploy F5 WAF for NGINX using Docker]({{< ref "/waf/install/docker.md" >}})
- [Deploy F5 WAF for NGINX using Kubernetes]({{< ref "/waf/install/kubernetes.md" >}})
- [Deploy F5 WAF for NGINX in a disconnected environment]({{< ref "/waf/install/disconnected-environment.md" >}})

F5 WAF for NGINX can be installed as a module for an existing NGINX Plus installation or installed alongside NGINX Plus in a new environment.

## Before you begin

To complete this guide, you will need the following prerequisites:

- An active F5 WAF for NGINX subscription (Purchased or trial)
- Credentials to the [MyF5 Customer Portal](https://account.f5.com/myf5), provided by email from F5, Inc.

Depending on your F5 WAF for NGINX version, you may also need:

- [Docker](https://docs.docker.com/get-started/get-docker/)

### Back-up existing NGINX files

If you are installing F5 WAF for NGINX on an active system with existing NGINX packages, use the following command to back up your configuration and log files:

```shell
sudo cp -a /etc/nginx /etc/nginx-plus-backup
sudo cp -a /var/log/nginx /var/log/nginx-plus-backup
```

### Download your subscription credentials

{{< include "licensing-and-reporting/download-certificates-from-myf5.md" >}}
