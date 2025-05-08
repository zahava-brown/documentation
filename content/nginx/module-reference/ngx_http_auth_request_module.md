---
title: "ngx_http_auth_request_module"
id: "/en/docs/http/ngx_http_auth_request_module.html"
toc: true
---

## `auth_request`

**Syntax:** *`uri`* | `off`

**Default:** off

**Contexts:** `http, server, location`

Enables authorization based on the result of a subrequest and sets
the URI to which the subrequest will be sent.

## `auth_request_set`

**Syntax:** *`$variable`* *`value`*

**Contexts:** `http, server, location`

Sets the request *`variable`* to the given
*`value`* after the authorization request completes.
The value may contain variables from the authorization request,
such as `$upstream_http_*`.

