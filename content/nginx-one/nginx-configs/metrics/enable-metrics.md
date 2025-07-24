---
# We use sentence case and present imperative tone
title: "Enable metrics"
# Weights are assigned in increments of 100: determines sorting order
weight: i00
# Creates a table of contents and sidebar, useful for large documents
toc: true
# Types have a 1:1 relationship with Hugo archetypes, so you shouldn't need to change this
nd-content-type: tutorial
# Intended for internal catalogue and search, case sensitive:
# Agent, N4Azure, NIC, NIM, NGF, NAP-DOS, NAP-WAF, NGINX One, NGINX+, Solutions, Unit
nd-product: NGINX-One
---

The NGINX One Console dashboard relies on APIs for NGINX Plus and NGINX Open Source Stub Status to report traffic and system metrics. The following sections show you how to enable those metrics.

### Enable NGINX Plus API

{{< include "/use-cases/monitoring/enable-nginx-plus-api.md" >}}

### Enable NGINX Open Source Stub Status API 

{{< include "/use-cases/monitoring/enable-nginx-oss-stub-status.md" >}}

### Include response codes

To collect response codes in your log files, set a `status_zone` in descired `location` blocks. Here's an
example that you could add to your NGINX configuration file:

```nginx
server {
    listen 8080;

    # - add response header
    add_header "x-datapath-instance" "<datapath_id>" always;

    default_type text/plain;

    location / {
        status_zone systest_loc_200;
        return 200 "hello from datapath <datapath_id>\n";
    }

    location /4xx {
        status_zone systest_loc_4xx;
        return 400 "fake 400 from datapath <datapath_id>\n";
    }

    location /5xx {
        status_zone systest_loc_5xx;
        return 500 "fake 500 from datapath <datapath_id>\n";
    }
}
```
