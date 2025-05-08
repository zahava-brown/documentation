---
title: "ngx_stream_access_module"
id: "/en/docs/stream/ngx_stream_access_module.html"
toc: true
---

## `allow`

**Syntax:** *`address`* | *`CIDR`* | `unix:` | `all`

**Contexts:** `stream, server`

Allows access for the specified network or address.
If the special value `unix:` is specified,
allows access for all UNIX-domain sockets.

## `deny`

**Syntax:** *`address`* | *`CIDR`* | `unix:` | `all`

**Contexts:** `stream, server`

Denies access for the specified network or address.
If the special value `unix:` is specified,
denies access for all UNIX-domain sockets.

