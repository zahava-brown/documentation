---
# We use sentence case and present imperative tone
title: "Deploy NGINX App Protect WAF using Kubernetes"
# Weights are assigned in increments of 100: determines sorting order
weight: 400
# Creates a table of contents and sidebar, useful for large documents
toc: false
nd-banner:
    enabled: true
    type: deprecation
    start-date: 2025-07-07
    md: /_banners/waf-v5-warning.md
# Types have a 1:1 relationship with Hugo archetypes, so you shouldn't need to change this
nd-content-type: how-to
# Intended for internal catalogue and search, case sensitive:
# Agent, N4Azure, NIC, NIM, NGF, NAP-DOS, NAP-WAF, NGINX One, NGINX+, Solutions, Unit
nd-product: NAP-WAF
---

{{< call-out "warning" "Information architecture note" >}}

The design intention for this page is to encapsulate the v5+ deployment methods, currently split between the following two pages:

- [Deploy NGINX App Protect WAF with Helm]({{< ref "/nap-waf/v5/admin-guide/deploy-with-helm.md" >}})
- [Deploy NGINX App Protect WAF with Manifests]({{< ref "/nap-waf/v5/admin-guide/deploy-with-manifests.md" >}})

In execution it will probably remain two separate files: the warning banner on top of the page will explicitly indicate what version of NAP the instructions are intended for.

{{</ call-out>}}