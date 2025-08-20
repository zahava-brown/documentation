---
# We use sentence case and present imperative tone
title: "Virtual environment"
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

This page describes how to install F5 WAF for NGINX on a virtual machine or bare metal environment. It explains the additional steps to install F5 WAF for NGINX in an environment with NGINX Plus.

## Before you begin

To complete this guide, you will need the following prerequisites:

- An active F5 WAF for NGINX subscription (Purchased or trial)
- A working [NGINX Plus installation]({{< ref "/nginx/admin-guide/installing-nginx/installing-nginx-plus.md" >}})

To review supported operating systems, please read the [Technical specifications]({{< ref "/waf/fundamentals/technical-specifications.md" >}}) guide.

## Platform-specific instructions

Navigate to your chosen operating system, which are alphabetically ordered.

The tabs are used to select steps specific to your F5 WAF for NGINX version.

### Alpine Linux

{{< tabs name="alpine-instructions" >}}

{{% tab name="V4" %}}

Add the F5 WAF for NGINX repository:

```shell
printf "https://pkgs.nginx.com/app-protect/alpine/v`egrep -o '^[0-9]+\.[0-9]+' /etc/alpine-release`/main\n" | sudo tee -a /etc/apk/repositories
printf "https://pkgs.nginx.com/app-protect-security-updates/alpine/v`egrep -o '^[0-9]+\.[0-9]+' /etc/alpine-release`/main\n" | sudo tee -a /etc/apk/repositories
```

Update the repositories, then install the F5 WAF for NGINX package and its dependencies:

```shell
sudo apk update
sudo apk add openssl ca-certificates app-protect
```

{{% /tab %}}

{{% tab name="V5" %}}

Add the F5 WAF for NGINX repository:

```shell
printf "https://pkgs.nginx.com/app-protect-x-plus/alpine/v`egrep -o '^[0-9]+\.[0-9]+' /etc/alpine-release`/main\n" | sudo tee -a /etc/apk/repositories
```

Update the repositories, then install the F5 WAF for NGINX package and its dependencies:

```shell
sudo apk update
sudo apk add openssl ca-certificates app-protect-module-plus
```

{{% /tab %}}

{{< /tabs >}}

### Amazon Linux

{{< tabs name="amazon-instructions" >}}

{{% tab name="V4" %}}

Add the F5 WAF for NGINX repository:

```shell
sudo wget -P /etc/yum.repos.d https://cs.nginx.com/static/files/app-protect-amazonlinux2023.repo
```

Add F5 WAF for NGINX dependencies:

```shell
sudo wget -P /etc/yum.repos.d https://cs.nginx.com/static/files/dependencies.amazonlinux2023.repo
```

Install the latest version of F5 WAF for NGINX:

```shell
sudo dnf install app-protect
```

{{% /tab %}}

{{% tab name="V5" %}}

Create a file for the F5 WAF for NGINX repository:

**/etc/yum.repos.d/app-protect-x-plus.repo**

```shell
[app-protect-x-plus]
name=nginx-app-protect repo
baseurl=https://pkgs.nginx.com/app-protect-x-plus/amzn/2023/$basearch/
sslclientcert=/etc/ssl/nginx/nginx-repo.crt
sslclientkey=/etc/ssl/nginx/nginx-repo.key
gpgcheck=0
enabled=1
```

Install the latest version of F5 WAF for NGINX:

```shell
sudo dnf install app-protect-module-plus
```

{{% /tab %}}

{{< /tabs >}}

### Debian 

{{< tabs name="debian-instructions" >}}

{{% tab name="V4" %}}

{{% /tab %}}

{{% tab name="V5" %}}


{{% /tab %}}

{{< /tabs >}}


### Oracle Linux

{{< tabs name="oracle-instructions" >}}

{{% tab name="V4" %}}

{{% /tab %}}

{{% tab name="V5" %}}


{{% /tab %}}

{{< /tabs >}}

### Ubuntu

{{< tabs name="ubuntu-instructions" >}}

{{% tab name="V4" %}}

{{% /tab %}}

{{% tab name="V5" %}}


{{% /tab %}}

{{< /tabs >}}

### RHEL / Rocky Linux

{{< tabs name="rhel-instructions" >}}

{{% tab name="V4" %}}

{{% /tab %}}

{{% tab name="V5" %}}


{{% /tab %}}

{{< /tabs >}}

## Update configuration files