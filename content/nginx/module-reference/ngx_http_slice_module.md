---
title: "ngx_http_slice_module"
id: "/en/docs/http/ngx_http_slice_module.html"
toc: true
---

## `slice`

**Syntax:** *`size`*

**Default:** 0

**Contexts:** `http, server, location`

Sets the *`size`* of the slice.
The zero value disables splitting responses into slices.
Note that a too low value may result in excessive memory usage
and opening a large number of files.

In order for a subrequest to return the required range,
the `$slice_range` variable should be
[passed](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_set_header) to
the proxied server as the `Range` request header field.
If
[caching](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cache)
is enabled, `$slice_range` should be added to the
[cache key](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cache_key)
and caching of responses with 206 status code should be
[enabled](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_cache_valid).

