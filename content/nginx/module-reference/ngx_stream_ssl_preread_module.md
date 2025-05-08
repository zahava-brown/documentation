---
title: "ngx_stream_ssl_preread_module"
id: "/en/docs/stream/ngx_stream_ssl_preread_module.html"
toc: true
---

## `ssl_preread`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `stream, server`

Enables extracting information from the ClientHello message at
the [preread](https://nginx.org/en/docs/stream/stream_processing.html#preread_phase) phase.

