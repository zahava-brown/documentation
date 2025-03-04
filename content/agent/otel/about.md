---
title: Overview
weight: 100
docs: DOCS-000
---

The F5 NGINX Agent v3 now includes an embedded OpenTelemetry (OTel) Collector, streamlining observability and metric collection for NGINX instances. With this feature, you can collect: 

* Metrics from NGINX Plus  and NGINX OSS 
* Host metrics  (CPU, memory, disk, and network activity) from VMs or Containers
     

This guide walks you through enabling and configuring the embedded OpenTelemetry Collector for metric export. 

# Key Benefits

* Seamless Integration: No need to deploy an external OpenTelemetry Collector. All components are embedded within the Agent for streamlined observability.
* Standardized Protocol: Support for OpenTelemetry standards ensures interoperability with a wide range of observability backends, including Jaeger, Prometheus, Splunk, and more.

# Configuration

## How to send data to an OTel Collector - VM

### Before you begin

- [NGINX One Console Getting Started]({{< relref "/nginx-one/getting-started" >}})
- NGINX OSS installed on a Virtual Machine
- NGINX Agent v3 is installed

### Setting Up the OTel Collector to Export Metrics to NGINX One Console
<!--  (NOTE For writer: Let the user know this is required and suggest options) -->

1. Enable NGINX Stub Status API to collect NGINX metrics in NGINX OSS. A sample Stub Status API configuration is shown below:
    
    ```vim
    server {
        listen 127.0.0.1:8080;
        location /api {
            stub_status;
            allow 127.0.0.1;
            deny all;
        }
    }
    ```

1. Edit the `/etc/nginx-agent/nginx-agent.conf` and add the following. 

    Make sure to replace your-data-plane-key-here with the real data plane key that you are using for your application or service.

    ```vim
    collector:
    receivers:
        host_metrics:
        collection_interval: 1m0s
        initial_delay: 1s
        scrapers:
            cpu: {}
            memory: {}
            disk: {}
            network: {}
            filesystem: {}
    processors:
        batch: {}
    exporters:
        otlp_exporters:
        - server:
            host: <saas-host>
            port: 443
            authenticator: headers_setter
            tls:
            skip_verify: false
    extensions:
        headers_setter:
        headers:
            - action: insert
            key: "authorization"
            value: "your-data-plane-key-here"
    ```


3. Restart the NGINX Agent service
    
    ```bash
        sudo systemctl restart nginx-agent
    ```

### Troubleshooting

1. Verify the OTel Collector is up and running 
    
    ```bash
    curl http://127.0.0.1:1337 | jq
    ```

The response should look like: 
    
```bash
{
    "status": "Server available",
    "upSince": "2025-02-19T17:51:25.195637946Z",
    "uptime": "5.601839172s"
}
```

2. Check the OpenTelemetry collector agent log

```bash
sudo tail -n 100 /var/log/nginx-agent/opentelemetry-collector-agent.log
```

3. Change the log level to debug