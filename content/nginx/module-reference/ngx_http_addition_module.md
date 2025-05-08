---
title: "ngx_http_addition_module"
id: "/en/docs/http/ngx_http_addition_module.html"
toc: true
---

## `add_before_body`

**Syntax:** *`uri`*

**Contexts:** `http, server, location`

Adds the text returned as a result of processing a given subrequest
before the response body.
An empty string (`""`) as a parameter cancels addition
inherited from the previous configuration level.

## `add_after_body`

**Syntax:** *`uri`*

**Contexts:** `http, server, location`

Adds the text returned as a result of processing a given subrequest
after the response body.
An empty string (`""`) as a parameter cancels addition
inherited from the previous configuration level.

## `addition_types`

**Syntax:** *`mime-type`* ...

**Default:** text/html

**Contexts:** `http, server, location`

Allows adding text in responses with the specified MIME types,
in addition to “`text/html`”.
The special value “`*`” matches any MIME type (0.8.29).

