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

{{< call-out "note" >}}

To use a V5-based package, you will also need to install [Docker](https://docs.docker.com/get-started/get-docker/).

{{< /call-out>}}

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

Once you have updated your configuration files, you can reload NGINX to apply the changes. You have two options depending on your environment:

- `nginx -s reload`
- `sudo systemctl reload nginx`

If you are using a V4 package, you have finished installing F5 WAF for NGINX and can look at [Post-installation checks](#post-installation-checks).

## Configure Docker services

{{< call-out "warning" >}}

This section **only** applies to V5 packages. 

Skip to [Post-installation checks](#post-installation-checks) if you're using a V4 package.

{{< /call-out>}}

F5 WAF for NGINX uses Docker containers for its services when installed with a V5 package, which requires some extra set-up steps.

First, create new directories for the services:

```shell
sudo mkdir -p /opt/app_protect/config /opt/app_protect/bd_config
```

Then assign new owners, with `101:101` as the default UID/GID

```shell
sudo chown -R 101:101 /opt/app_protect/
```

### Configure Docker for the F5 Container Registry

Create a directory and copy your certificate and key to this directory:

```shell
mkdir -p /etc/docker/certs.d/private-registry.nginx.com
cp <path-to-your-nginx-repo.crt> /etc/docker/certs.d/private-registry.nginx.com/client.cert
cp <path-to-your-nginx-repo.key> /etc/docker/certs.d/private-registry.nginx.com/client.key
```

### Download Docker images

Download the `waf-enforcer` and `waf-config-mgr` images. 

Replace `5.2.0` with the release version you are deploying.

```shell
docker pull private-registry.nginx.com/nap/waf-enforcer:5.2.0
docker pull private-registry.nginx.com/nap/waf-config-mgr:5.2.0
```

### Create and run a Docker Compose file

Create a _docker-compose.yml_ file with the following contents in your host environment, replacing the image tag as appropriate.

```yaml
services:
  waf-enforcer:
    container_name: waf-enforcer
    image: waf-enforcer:5.2.0
    environment:
      - ENFORCER_PORT=50000
    ports:
      - "50000:50000"
    volumes:
      - /opt/app_protect/bd_config:/opt/app_protect/bd_config
    networks:
      - waf_network
    restart: always

  waf-config-mgr:
    container_name: waf-config-mgr
    image: waf-config-mgr:5.2.0
    volumes:
      - /opt/app_protect/bd_config:/opt/app_protect/bd_config
      - /opt/app_protect/config:/opt/app_protect/config
      - /etc/app_protect/conf:/etc/app_protect/conf
    restart: always
    network_mode: none
    depends_on:
      waf-enforcer:
        condition: service_started

networks:
  waf_network:
    driver: bridge
```

{{< call-out "caution" >}}

In some operating systems, security mechanisms like SELinux or AppArmor are enabled by default, potentially blocking necessary file access for the nginx process and waf-config-mgr and waf-enforcer containers.

To ensure NGINX App Protect WAF operates smoothly without compromising security, consider setting up a custom SELinux policy or AppArmor profile. 

For short-term troubleshooting, you may use permissive (SELinux) or complain (AppArmor) mode to avoid these restrictions, but this is inadvisable for prolonged use.

{{< /call-out >}}

To start the F5 WAF for NGINX services, use `docker compose up` in the same folder as the _docker-compose.yml_ file:

```shell
sudo docker compose up -d
```


## Post-installation checks

Use the following steps to ensure that F5 WAF for NGINX enforcement is operational.

Check that the three processes for F5 WAF for NGINX are running using `ps aux`:

- _bd-socket-plugin_
- _nginx: master process_
- _nginx: worker process_

```shell
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         8  1.3  2.4 3486948 399092 ?      Sl   09:11   0:02 /usr/share/ts/bin/bd-socket-plugin tmm_count 4 proc_cpuinfo_cpu_mhz 2000000 total_xml_memory 307200000 total_umu_max_size 3129344 sys_max_account_id 1024 no_static_config
root        14  0.0  0.1  71060 26680 ?        S    09:11   0:00 nginx: master process /usr/sbin/nginx -c /tmp/policy/test_nginx.conf -g daemon off;
root        26  0.0  0.3  99236 52092 ?        S    09:12   0:00 nginx: worker process
root        28  0.0  0.0  11788  2920 pts/0    Ss   09:12   0:00 bash
root        43  0.0  0.0  47460  3412 pts/0    R+   09:14   0:00 ps aux
```

Verify there are no errors in the file `/var/log/nginx/error.log` and that the policy compiled successfully:

```none
2020/05/10 13:21:04 [notice] 402#402: APP_PROTECT { "event": "configuration_load_start", "configSetFile": "/opt/f5waf/config/config_set.json" }
2020/05/10 13:21:04 [notice] 402#402: APP_PROTECT policy 'app_protect_default_policy' from: /etc/app_protect/conf/NginxDefaultPolicy.json compiled successfully
2020/05/10 13:21:04 [notice] 402#402: APP_PROTECT { "event": "configuration_load_success", "software_version": "1.1.1", "attack_signatures_package":{"revision_datetime":"2019-07-16T12:21:31Z"},"completed_successfully":true}
2020/05/10 13:21:04 [notice] 402#402: using the "epoll" event method
2020/05/10 13:21:04 [notice] 402#402: nginx/1.17.6 (nginx-plus-r20)
2020/05/10 13:21:04 [notice] 402#402: built by gcc 4.8.5 20150623 (Red Hat 4.8.5-36) (GCC)
2020/05/10 13:21:04 [notice] 402#402: OS: Linux 3.10.0-957.27.2.el7.x86_64
2020/05/10 13:21:04 [notice] 402#402: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2020/05/10 13:21:04 [notice] 406#406: start worker processes
2020/05/10 13:21:04 [notice] 406#406: start worker process 407
```

Check that sending an attack signature in a request returns a response block page containing a support ID:

```shell
Request:
http://10.240.185.211/?a=<script>

Response:
The requested URL was rejected. Please consult with your administrator.

Your support ID is: 9847191526422998597

[Go Back]
```

If you case your policy includes JSON/XML profiles, check `/var/log/app_protect/bd-socket-plugin.log` for possible errors:

```shell
grep '|ERR' /var/log/app_protect/bd-socket-plugin.log
```

Verify that Enforcement functionality is working by checking the following request is rejected:

```shell
curl "localhost/<script>"
```

## Next steps

Once you have successfully installed F5 WAf for NGINX, there are some topics you may want to follow afterwards:

- [Converter tools]({{< ref "/waf/tools/converter.md" >}}), to convert existing resources from a BIG-IP environment
- [Configure policies]({{< ref "/waf/policies/configuration.md" >}}), to begin customizing your deployment
- [Changelog]({{< ref "/waf/changelog.md" >}}), to view information from the latest releases