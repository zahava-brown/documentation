---
title: "ngx_http_status_module"
id: "/en/docs/http/ngx_http_status_module.html"
toc: true
---

## `status`

**Contexts:** `location`

The status information will be accessible from the surrounding location.
Access to this location should be
[limited](https://nginx.org/en/docs/http/ngx_http_core_module.html#satisfy).

## `status_format`

**Syntax:** `json`

**Default:** json

**Contexts:** `http, server, location`

By default, status information is output in the JSON format.

Alternatively, data may be output as JSONP.
The *`callback`* parameter specifies the name of a callback function.
Parameter value can contain variables.
If parameter is omitted, or the computed value is an empty string,
then “`ngx_status_jsonp_callback`” is used.

## `status_zone`

**Syntax:** *`zone`*

**Contexts:** `server`

Enables collection of virtual
[http](https://nginx.org/en/docs/http/ngx_http_core_module.html#server)
or
[stream](https://nginx.org/en/docs/stream/ngx_stream_core_module.html#server)
(1.7.11) server status information in the specified *`zone`*.
Several servers may share the same zone.

