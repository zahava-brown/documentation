---
title: "ngx_http_api_module"
id: "/en/docs/http/ngx_http_api_module.html"
toc: true
---

## `api`

**Syntax:** [`write`=`on`|`off`]

**Contexts:** `location`

Turns on the REST API interface in the surrounding location.
Access to this location should be
[limited](https://nginx.org/en/docs/http/ngx_http_core_module.html#satisfy).

The `write` parameter determines whether the API
is read-only or read-write.
By default, the API is read-only.

All API requests should contain a supported API version in the URI.
If the request URI equals the location prefix,
the list of supported API versions is returned.
The current API version is “`9`”.

The optional “`fields`” argument in the request line
specifies which fields of the requested objects will be output:
```
http://127.0.0.1/api/9/nginx?fields=version,build
```

## `status_zone`

**Syntax:** *`zone`*

**Contexts:** `server, location, if in location`

Enables collection of virtual
[http](https://nginx.org/en/docs/http/ngx_http_core_module.html#server)
or
[stream](https://nginx.org/en/docs/stream/ngx_stream_core_module.html#server)
server status information in the specified *`zone`*.
Several servers may share the same zone.

Starting from 1.17.0, status information can be collected
per [`location`](https://nginx.org/en/docs/http/ngx_http_core_module.html#location).
The special value `off` disables statistics collection
in nested location blocks.
Note that the statistics is collected
in the context of a location where processing ends.
It may be different from the original location, if an
[internal redirect](https://nginx.org/en/docs/http/ngx_http_core_module.html#internal) happens during request processing.

