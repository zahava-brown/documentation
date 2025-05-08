---
title: "ngx_http_ssi_module"
id: "/en/docs/http/ngx_http_ssi_module.html"
toc: true
---

## `ssi`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location, if in location`

Enables or disables processing of SSI commands in responses.

## `ssi_last_modified`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location`

Allows preserving the "Last-Modified" header field
from the original response during SSI processing
to facilitate response caching.

By default, the header field is removed as contents of the response
are modified during processing and may contain dynamically generated elements
or parts that are changed independently of the original response.

## `ssi_min_file_chunk`

**Syntax:** `size`

**Default:** 1k

**Contexts:** `http, server, location`

Sets the minimum *`size`* for parts of a response stored on disk,
starting from which it makes sense to send them using
[`sendfile`](https://nginx.org/en/docs/http/ngx_http_core_module.html#sendfile).

## `ssi_silent_errors`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location`

If enabled, suppresses the output of the
“`[an error occurred while processing the directive]`”
string if an error occurred during SSI processing.

## `ssi_types`

**Syntax:** *`mime-type`* ...

**Default:** text/html

**Contexts:** `http, server, location`

Enables processing of SSI commands in responses with the specified MIME types
in addition to “`text/html`”.
The special value “`*`” matches any MIME type (0.8.29).

## `ssi_value_length`

**Syntax:** *`length`*

**Default:** 256

**Contexts:** `http, server, location`

Sets the maximum length of parameter values in SSI commands.

