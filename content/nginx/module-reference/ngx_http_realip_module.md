---
title: "ngx_http_realip_module"
id: "/en/docs/http/ngx_http_realip_module.html"
toc: true
---

## `set_real_ip_from`

**Syntax:** *`address`* | *`CIDR`* | `unix:`

**Contexts:** `http, server, location`

Defines trusted addresses that are known to send correct
replacement addresses.
If the special value `unix:` is specified,
all UNIX-domain sockets will be trusted.
Trusted addresses may also be specified using a hostname (1.13.1).
> IPv6 addresses are supported starting from versions 1.3.0 and 1.2.1.

## `real_ip_header`

**Syntax:** *`field`* | `X-Real-IP` | `X-Forwarded-For` | `proxy_protocol`

**Default:** X-Real-IP

**Contexts:** `http, server, location`

Defines the request header field
whose value will be used to replace the client address.

The request header field value that contains an optional port
is also used to replace the client port (1.11.0).
The address and port should be specified according to
[RFC 3986](https://datatracker.ietf.org/doc/html/rfc3986).

The `proxy_protocol` parameter (1.5.12) changes
the client address to the one from the PROXY protocol header.
The PROXY protocol must be previously enabled by setting the
`proxy_protocol` parameter
in the [`listen`](https://nginx.org/en/docs/http/ngx_http_core_module.html#listen) directive.

## `real_ip_recursive`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location`

If recursive search is disabled, the original client address that
matches one of the trusted addresses is replaced by the last
address sent in the request header field defined by the
[`real_ip_header`](https://nginx.org/en/docs/http/ngx_http_realip_module.html#real_ip_header) directive.
If recursive search is enabled, the original client address that
matches one of the trusted addresses is replaced by the last
non-trusted address sent in the request header field.

