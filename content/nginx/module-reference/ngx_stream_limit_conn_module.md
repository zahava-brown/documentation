---
title: "ngx_stream_limit_conn_module"
id: "/en/docs/stream/ngx_stream_limit_conn_module.html"
toc: true
---

## `limit_conn`

**Syntax:** *`zone`* *`number`*

**Contexts:** `stream, server`

Sets the shared memory zone
and the maximum allowed number of connections for a given key value.
When this limit is exceeded, the server will close the connection.
For example, the directives
```
limit_conn_zone $binary_remote_addr zone=addr:10m;

server {
    ...
    limit_conn addr 1;
}
```
allow only one connection per an IP address at a time.

When several `limit_conn` directives are specified,
any configured limit will apply.

These directives are inherited from the previous configuration level
if and only if there are no `limit_conn` directives
defined on the current level.

## `limit_conn_dry_run`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `stream, server`

Enables the dry run mode.
In this mode, the number of connections is not limited, however,
in the shared memory zone, the number of excessive connections is accounted
as usual.

## `limit_conn_log_level`

**Syntax:** `info` | `notice` | `warn` | `error`

**Default:** error

**Contexts:** `stream, server`

Sets the desired logging level for cases when the server
limits the number of connections.

## `limit_conn_zone`

**Syntax:** *`key`* `zone`=*`name`*:*`size`*

**Contexts:** `stream`

Sets parameters for a shared memory zone
that will keep states for various keys.
In particular, the state includes the current number of connections.
The *`key`* can contain text, variables,
and their combinations (1.11.2).
Connections with an empty key value are not accounted.
Usage example:
```
limit_conn_zone $binary_remote_addr zone=addr:10m;
```
Here, the key is a client IP address set by the
`$binary_remote_addr` variable.
The size of `$binary_remote_addr`
is 4 bytes for IPv4 addresses or 16 bytes for IPv6 addresses.
The stored state always occupies 32 or 64 bytes
on 32-bit platforms and 64 bytes on 64-bit platforms.
One megabyte zone can keep about 32 thousand 32-byte states
or about 16 thousand 64-byte states.
If the zone storage is exhausted, the server will close the connection.

> Additionally, as part of our
> [commercial subscription](https://nginx.com/products/),
> the
> [status information](https://nginx.org/en/docs/http/ngx_http_api_module.html#stream_limit_conns_)
> for each such shared memory zone can be
> [obtained](https://nginx.org/en/docs/http/ngx_http_api_module.html#getStreamLimitConnZone) or
> [reset](https://nginx.org/en/docs/http/ngx_http_api_module.html#deleteStreamLimitConnZoneStat)
> with the [API](https://nginx.org/en/docs/http/ngx_http_api_module.html) since 1.17.7.

