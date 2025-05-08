---
title: "ngx_http_limit_conn_module"
id: "/en/docs/http/ngx_http_limit_conn_module.html"
toc: true
---

## `limit_conn`

**Syntax:** *`zone`* *`number`*

**Contexts:** `http, server, location`

Sets the shared memory zone
and the maximum allowed number of connections for a given key value.
When this limit is exceeded, the server will return the
[error](https://nginx.org/en/docs/http/ngx_http_limit_conn_module.html#limit_conn_status)
in reply to a request.
For example, the directives
```
limit_conn_zone $binary_remote_addr zone=addr:10m;

server {
    location /download/ {
        limit_conn addr 1;
    }
```
allow only one connection per an IP address at a time.
> In HTTP/2 and HTTP/3,
> each concurrent request is considered a separate connection.

There could be several `limit_conn` directives.
For example, the following configuration will limit the number
of connections to the server per a client IP and, at the same time,
the total number of connections to the virtual server:
```
limit_conn_zone $binary_remote_addr zone=perip:10m;
limit_conn_zone $server_name zone=perserver:10m;

server {
    ...
    limit_conn perip 10;
    limit_conn perserver 100;
}
```

These directives are inherited from the previous configuration level
if and only if there are no `limit_conn` directives
defined on the current level.

## `limit_conn_dry_run`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location`

Enables the dry run mode.
In this mode, the number of connections is not limited, however,
in the shared memory zone, the number of excessive connections is accounted
as usual.

## `limit_conn_log_level`

**Syntax:** `info` | `notice` | `warn` | `error`

**Default:** error

**Contexts:** `http, server, location`

Sets the desired logging level for cases when the server
limits the number of connections.

## `limit_conn_status`

**Syntax:** *`code`*

**Default:** 503

**Contexts:** `http, server, location`

Sets the status code to return in response to rejected requests.

## `limit_conn_zone`

**Syntax:** *`key`* `zone`=*`name`*:*`size`*

**Contexts:** `http`

Sets parameters for a shared memory zone
that will keep states for various keys.
In particular, the state includes the current number of connections.
The *`key`* can contain text, variables, and their combination.
Requests with an empty key value are not accounted.
> Prior to version 1.7.6, a *`key`* could contain exactly one variable.

Usage example:
```
limit_conn_zone $binary_remote_addr zone=addr:10m;
```
Here, a client IP address serves as a key.
Note that instead of `$remote_addr`, the
`$binary_remote_addr` variable is used here.
The `$remote_addr` variable’s size can
vary from 7 to 15 bytes.
The stored state occupies either
32 or 64 bytes of memory on 32-bit platforms and always 64
bytes on 64-bit platforms.
The `$binary_remote_addr` variable’s size
is always 4 bytes for IPv4 addresses or 16 bytes for IPv6 addresses.
The stored state always occupies 32 or 64 bytes
on 32-bit platforms and 64 bytes on 64-bit platforms.
One megabyte zone can keep about 32 thousand 32-byte states
or about 16 thousand 64-byte states.
If the zone storage is exhausted, the server will return the
[error](https://nginx.org/en/docs/http/ngx_http_limit_conn_module.html#limit_conn_status)
to all further requests.

> Additionally, as part of our
> [commercial subscription](https://nginx.com/products/),
> the
> [status information](https://nginx.org/en/docs/http/ngx_http_api_module.html#http_limit_conns_)
> for each such shared memory zone can be
> [obtained](https://nginx.org/en/docs/http/ngx_http_api_module.html#getHttpLimitConnZone) or
> [reset](https://nginx.org/en/docs/http/ngx_http_api_module.html#deleteHttpLimitConnZoneStat)
> with the [API](https://nginx.org/en/docs/http/ngx_http_api_module.html) since 1.17.7.

## `limit_zone`

**Syntax:** *`name`* *`$variable`* *`size`*

**Contexts:** `http`

This directive was made obsolete in version 1.1.8
and was removed in version 1.7.6.
An equivalent [`limit_conn_zone`](https://nginx.org/en/docs/http/ngx_http_limit_conn_module.html#limit_conn_zone) directive
with a changed syntax should be used instead:
> `limit_conn_zone`
> *`$variable`*
> `zone`=*`name`*:*`size`*;

