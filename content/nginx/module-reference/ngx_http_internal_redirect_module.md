---
title: "ngx_http_internal_redirect_module"
id: "/en/docs/http/ngx_http_internal_redirect_module.html"
toc: true
---

## `internal_redirect`

**Syntax:** *`uri`*

**Contexts:** `server, location`

Sets the URI for internal redirection of the request.
It is also possible to use a
[named location](https://nginx.org/en/docs/http/ngx_http_core_module.html#location_named)
instead of the URI.
The *`uri`* value can contain variables.
If the *`uri`* value is empty,
then the redirect will not be made.

