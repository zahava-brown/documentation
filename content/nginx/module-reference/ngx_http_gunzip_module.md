---
title: "ngx_http_gunzip_module"
id: "/en/docs/http/ngx_http_gunzip_module.html"
toc: true
---

## `gunzip`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location`

Enables or disables decompression of gzipped responses
for clients that lack gzip support.
If enabled, the following directives are also taken into account
when determining if clients support gzip:
[`gzip_http_version`](https://nginx.org/en/docs/http/ngx_http_gzip_module.html#gzip_http_version),
[`gzip_proxied`](https://nginx.org/en/docs/http/ngx_http_gzip_module.html#gzip_proxied), and
[`gzip_disable`](https://nginx.org/en/docs/http/ngx_http_gzip_module.html#gzip_disable).
See also the [`gzip_vary`](https://nginx.org/en/docs/http/ngx_http_gzip_module.html#gzip_vary) directive.

## `gunzip_buffers`

**Syntax:** *`number`* *`size`*

**Default:** 32 4k|16 8k

**Contexts:** `http, server, location`

Sets the *`number`* and *`size`* of buffers
used to decompress a response.
By default, the buffer size is equal to one memory page.
This is either 4K or 8K, depending on a platform.

