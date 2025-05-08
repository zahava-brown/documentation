---
title: "ngx_http_sub_module"
id: "/en/docs/http/ngx_http_sub_module.html"
toc: true
---

## `sub_filter`

**Syntax:** *`string`* *`replacement`*

**Contexts:** `http, server, location`

Sets a string to replace and a replacement string.
The string to replace is matched ignoring the case.
The string to replace (1.9.4) and replacement string can contain variables.
Several `sub_filter` directives
can be specified on the same configuration level (1.9.4).
These directives are inherited from the previous configuration level
if and only if there are no `sub_filter` directives
defined on the current level.

## `sub_filter_last_modified`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location`

Allows preserving the "Last-Modified" header field
from the original response during replacement
to facilitate response caching.

By default, the header field is removed as contents of the response
are modified during processing.

## `sub_filter_once`

**Syntax:** `on` | `off`

**Default:** on

**Contexts:** `http, server, location`

Indicates whether to look for each string to replace
once or repeatedly.

## `sub_filter_types`

**Syntax:** *`mime-type`* ...

**Default:** text/html

**Contexts:** `http, server, location`

Enables string replacement in responses with the specified MIME types
in addition to “`text/html`”.
The special value “`*`” matches any MIME type (0.8.29).

