---
title: Set up NGINX metrics monitoring
nd-subtitle: 
url: /solutions/metrics/
nd-landing-page: true
nd-content-type: landing-page
nd-product: NGINX Plus
---

Monitoring is essential for understanding the health, performance, and reliability of your NGINX deployment.  

NGINX provides different levels of observability depending on which product you use:  

- NGINX Open Source includes basic traffic and connection metrics through the [stub_status module](https://nginx.org/en/docs/http/ngx_http_stub_status_module.html).  
- NGINX Plus provides the [NGINX Plus API](https://nginx.org/en/docs/http/ngx_http_api_module.html), which exposes detailed JSON-formatted metrics for connections, server zones, upstreams, caching, SSL/TLS, and more.  

Use this guide to:  
- Learn what metrics mean in NGINX and why monitoring matters.  
- Compare the metrics available in NGINX Open Source and NGINX Plus.  
- Follow step-by-step instructions to configure metrics collection.  
- Integrate NGINX with observability tools such as Prometheus, Grafana, NGINX Amplify, or Datadog.  

Choose an option below to get started.  

---

{{<card-layout>}}
  {{<card-section showAsCards="true">}}

    {{<card title="Monitoring overview" titleUrl="/solutions/monitoring/overview/" >}}
      Learn what metrics mean in NGINX, why monitoring matters, and how NGINX Open Source and NGINX Plus differ in their observability features.
    {{</card>}}

    {{<card title="Compare NGINX Open Source and NGINX Plus metrics" titleUrl="/solutions/monitoring/compare-metrics/" >}}
      See a side-by-side comparison table of available metrics in NGINX Open Source vs NGINX Plus, with quick links into setup instructions.
    {{</card>}}

    {{<card title="Configure metrics in NGINX Open Source" titleUrl="/solutions/monitoring/configure-open-source/" >}}
      Step-by-step instructions for enabling the <code>stub_status</code> module in NGINX Open Source, including configuration snippets and sample outputs.
    {{</card>}}

    {{<card title="Configure metrics in NGINX Plus" titleUrl="/solutions/monitoring/configure-plus/" >}}
      Learn how to enable and use the NGINX Plus API for detailed monitoring and integrations.
    {{</card>}}

    {{<card title="Integrate with observability tools" titleUrl="/solutions/monitoring/integrations/" >}}
      Connect NGINX metrics to Prometheus, Grafana, NGINX Amplify, Datadog, and other observability platforms.
    {{</card>}}

  {{</card-section>}}
{{</card-layout>}}