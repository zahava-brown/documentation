---
# We use sentence case and present imperative tone
title: "Technical specifications"
# Weights are assigned in increments of 100: determines sorting order
weight: 200
# Creates a table of contents and sidebar, useful for large documents
toc: false
# Types have a 1:1 relationship with Hugo archetypes, so you shouldn't need to change this
nd-content-type: how-to
# Intended for internal catalogue and search, case sensitive:
# Agent, N4Azure, NIC, NIM, NGF, NAP-DOS, NAP-WAF, NGINX One, NGINX+, Solutions, Unit
nd-product: NAP-WAF
---

{{< call-out "warning" "Information architecture note" >}}

The design intention for this page is to act as a single source of truth for supported operating systems and version compatibility.

It follows a design pattern set by other NGINX product sets, showing various compatibility matrices:

- [NGINX Plus]({{< ref "/nginx/technical-specs.md" >}})
- [NGINX Instance Manager]({{< ref "/nim/fundamentals/tech-specs.md" >}})
- [NGINX Ingress Controller]({{< ref "/nic/technical-specifications.md" >}})

It is also where information about the [Supported Security Policy Features]({{< ref "/nap-waf/v4/configuration-guide/configuration.md#supported-security-policy-features" >}}) could be referenced, though most of that detail will instead be kept in the new top-level "Policies" section.

{{</ call-out>}}