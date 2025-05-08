---
title: "ngx_http_access_module"
id: "/en/docs/http/ngx_http_access_module.html"
toc: true
---

## `allow`

**Syntax:** *`address`* | *`CIDR`* | `unix:` | `all`

**Contexts:** `http, server, location, limit_except`

Allows access for the specified network or address.
If the special value `unix:` is specified (1.5.1),
allows access for all UNIX-domain sockets.

## `deny`

**Syntax:** *`address`* | *`CIDR`* | `unix:` | `all`

**Contexts:** `http, server, location, limit_except`

Denies access for the specified network or address.
If the special value `unix:` is specified (1.5.1),
denies access for all UNIX-domain sockets.

