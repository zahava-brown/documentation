---
title: "ngx_http_keyval_module"
id: "/en/docs/http/ngx_http_keyval_module.html"
toc: true
---

## `keyval`

**Syntax:** *`key`* *`$variable`* `zone`=*`name`*

**Contexts:** `http`

Creates a new *`$variable`* whose value
is looked up by the *`key`* in the key-value database.
Matching rules are defined by the
[`type`](https://nginx.org/en/docs/http/ngx_http_keyval_module.html#keyval_type) parameter of the
[`keyval_zone`](https://nginx.org/en/docs/http/ngx_http_keyval_module.html#keyval_zone) directive.
The database is stored in a shared memory zone
specified by the `zone` parameter.

## `keyval_zone`

**Syntax:** `zone`=*`name`*:*`size`* [`state`=*`file`*] [`timeout`=*`time`*] [`type`=`string`|`ip`|`prefix`] [`sync`]

**Contexts:** `http`

Sets the *`name`* and *`size`* of the shared memory zone
that keeps the key-value database.
Key-value pairs are managed by the
[API](https://nginx.org/en/docs/http/ngx_http_api_module.html#http_keyvals_).

The optional `state` parameter specifies a *`file`*
that keeps the current state of the key-value database in the JSON format
and makes it persistent across nginx restarts.
Changing the file content directly should be avoided.

Examples:
```
keyval_zone zone=one:32k state=/var/lib/nginx/state/one.keyval; # path for Linux
keyval_zone zone=one:32k state=/var/db/nginx/state/one.keyval;  # path for FreeBSD
```

The optional `timeout` parameter (1.15.0) sets
the time after which key-value pairs are removed from the zone.

The optional `type` parameter (1.17.1) activates
an extra index optimized for matching the key of a certain type
and defines matching rules when evaluating
a [keyval](https://nginx.org/en/docs/http/ngx_http_keyval_module.html#keyval) `$variable`.
> The index is stored in the same shared memory zone
> and thus requires additional storage.


- `type=string`

    default, no index is enabled;
    variable lookup is performed using exact match
    of the record key and a search key
- `type=ip`

    the search key is the textual representation of IPv4 or IPv6 address
    or CIDR range;
    to match a record key, the search key must belong to a subnet
    specified by a record key or exactly match an IP address
- `type=prefix`

    variable lookup is performed using prefix match
    of a record key and a search key (1.17.5);
    to match a record key, the record key must be a prefix of the search key

The optional `sync` parameter (1.15.0) enables
[synchronization](https://nginx.org/en/docs/stream/ngx_stream_zone_sync_module.html#zone_sync)
of the shared memory zone.
The synchronization requires the
`timeout` parameter to be set.
> If the synchronization is enabled, removal of key-value pairs (no matter
> [one](https://nginx.org/en/docs/http/ngx_http_api_module.html#patchHttpKeyvalZoneKeyValue)
> or
> [all](https://nginx.org/en/docs/http/ngx_http_api_module.html#deleteHttpKeyvalZoneData))
> will be performed only on a target cluster node.
> The same key-value pairs on other cluster nodes
> will be removed upon `timeout`.

