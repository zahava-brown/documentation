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

Install the F5 WAF for NGINX package and its dependencies:

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

Install the F5 WAF for NGINX package and its dependencies:

```shell
sudo dnf install app-protect-module-plus
```

{{% /tab %}}

{{< /tabs >}}

### Debian

{{< tabs name="debian-instructions" >}}

{{% tab name="V4" %}}

Add the F5 WAF for NGINX repositories:

```shell
printf "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
https://pkgs.nginx.com/app-protect/debian `lsb_release -cs` nginx-plus\n" | \
sudo tee /etc/apt/sources.list.d/nginx-app-protect.list

printf "deb [signed-by=/usr/share/keyrings/app-protect-security-updates.gpg] \
https://pkgs.nginx.com/app-protect-security-updates/debian `lsb_release -cs` nginx-plus\n" | \
sudo tee /etc/apt/sources.list.d/app-protect-security-updates.list
```

Update the repositories, then install the F5 WAF for NGINX package and its dependencies:

```shell
sudo apt-get update
sudo apt-get install app-protect
```

{{% /tab %}}

{{% tab name="V5" %}}

Add the F5 WAF for NGINX repository:

```shell
printf "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
https://pkgs.nginx.com/app-protect-x-plus/debian `lsb_release -cs` nginx-plus\n" | \
sudo tee /etc/apt/sources.list.d/nginx-app-protect.list
```

Update the repositories, then install the F5 WAF for NGINX package and its dependencies:

```shell
sudo apt-get update
sudo apt-get install app-protect-module-plus
```

{{% /tab %}}

{{< /tabs >}}


### Oracle Linux / RHEL / Rocky Linux 8

{{< call-out "important" >}}

These instructions apply to Oracle Linux 8.1, and RHEL / Rocky Linux 8.

Their packages are identical due to their similar architecture.

{{< /call-out>}}

{{< tabs name="oracle-instructions" >}}

{{% tab name="V4" %}}

Add the F5 WAF for NGINX repository:

```shell
sudo wget -P /etc/yum.repos.d https://cs.nginx.com/static/files/app-protect-8.repo
```

Add F5 WAF for NGINX dependencies:

```shell
sudo wget -P /etc/yum.repos.d https://cs.nginx.com/static/files/dependencies.repo
```

Enable the _ol8_codeready_builder_ repository:

```shell
sudo dnf config-manager --set-enabled ol8_codeready_builder
```

Install the F5 WAF for NGINX package and its dependencies:

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
baseurl=https://pkgs.nginx.com/app-protect-x-plus/centos/8/$basearch/
sslclientcert=/etc/ssl/nginx/nginx-repo.crt
sslclientkey=/etc/ssl/nginx/nginx-repo.key
gpgcheck=0
enabled=1
```

Install the F5 WAF for NGINX package and its dependencies:

```shell
sudo dnf install app-protect-module-plus
```

{{% /tab %}}

{{< /tabs >}}

### Ubuntu

{{< tabs name="ubuntu-instructions" >}}

{{% tab name="V4" %}}

Add the F5 WAF for NGINX repositories:

```shell
printf "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
https://pkgs.nginx.com/app-protect/ubuntu `lsb_release -cs` nginx-plus\n" | \
sudo tee /etc/apt/sources.list.d/nginx-app-protect.list

printf "deb [signed-by=/usr/share/keyrings/app-protect-security-updates.gpg] \
https://pkgs.nginx.com/app-protect-security-updates/ubuntu `lsb_release -cs` nginx-plus\n" | \
sudo tee /etc/apt/sources.list.d/app-protect-security-updates.list
```

Update the repositories, then install the F5 WAF for NGINX package and its dependencies:

```shell
sudo apt-get update
sudo apt-get install app-protect
```

{{% /tab %}}

{{% tab name="V5" %}}

Add the F5 WAF for NGINX repositories:

```shell
printf "deb [signed-by=/usr/share/keyrings/nginx-archive-keyring.gpg] \
https://pkgs.nginx.com/app-protect-x-plus/ubuntu `lsb_release -cs` nginx-plus\n" | \
sudo tee /etc/apt/sources.list.d/nginx-app-protect.list
```

Update the repository, then install the F5 WAF for NGINX package and its dependencies:

```shell
sudo apt-get update
sudo apt-get install app-protect-module-plus
```

{{% /tab %}}

{{< /tabs >}}

### RHEL / Rocky Linux 9

{{< tabs name="rhel-instructions" >}}

{{% tab name="V4" %}}

Add the F5 WAF for NGINX repository:

```shell
sudo wget -P /etc/yum.repos.d https://cs.nginx.com/static/files/app-protect-9.repo
```

Add F5 WAF for NGINX dependencies:

```shell
sudo wget -P /etc/yum.repos.d https://cs.nginx.com/static/files/dependencies.repo
```

Enable the _codeready-builder_ repository:

```shell
sudo subscription-manager repos --enable codeready-builder-for-rhel-9-x86_64-rpms
```

Install the F5 WAF for NGINX package and its dependencies:

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
baseurl=https://pkgs.nginx.com/app-protect-x-plus/centos/8/$basearch/
sslclientcert=/etc/ssl/nginx/nginx-repo.crt
sslclientkey=/etc/ssl/nginx/nginx-repo.key
gpgcheck=0
enabled=1
```

Install the F5 WAF for NGINX package and its dependencies:

```shell
sudo dnf install app-protect-module-plus
```

{{% /tab %}}

{{< /tabs >}}

## Update configuration files

Once you have installed F5 WAF for NGINX, you must load it as a module in the main context of your NGINX configuration.

```nginx
load_module modules/ngx_http_app_protect_module.so;
```

The Enforcer address must be added at the _http_ context:

```nginx
app_protect_enforcer_address 127.0.0.1:50000;
```

And finally, F5 WAF for NGINX can enabled on a _http_, _server_ or _location_ context:

```nginx
app_protect_enable on;
```

{{< call-out "warning" >}}

You should only enable F5 WAF for NGINX on _proxy_pass_ and _grpc_pass_ locations.

{{< /call-out >}}

Here are two examples of how these additions could look in configuration files:

{{<tabs name="example-configuration-files">}}

{{% tab name="nginx.conf" %}}

`/etc/nginx/nginx.conf`

{{< include "waf/nginx-conf-localhost.md" >}}

{{% /tab %}}

{{% tab name="default.conf" %}}

`/etc/nginx/conf.d/default.conf`

{{< include "waf/default-conf-localhost.md" >}}

{{%/tab%}}

{{< /tabs >}}
