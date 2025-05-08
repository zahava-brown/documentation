---
title: "ngx_http_autoindex_module"
id: "/en/docs/http/ngx_http_autoindex_module.html"
toc: true
---

## `autoindex`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location`

Enables or disables the directory listing output.

## `autoindex_exact_size`

**Syntax:** `on` | `off`

**Default:** on

**Contexts:** `http, server, location`

For the HTML [format](https://nginx.org/en/docs/http/ngx_http_autoindex_module.html#autoindex_format),
specifies whether exact file sizes should be output in the directory listing,
or rather rounded to kilobytes, megabytes, and gigabytes.

## `autoindex_format`

**Syntax:** `html` | `xml` | `json` | `jsonp`

**Default:** html

**Contexts:** `http, server, location`

Sets the format of a directory listing.

When the JSONP format is used, the name of a callback function is set
with the `callback` request argument.
If the argument is missing or has an empty value,
then the JSON format is used.

The XML output can be transformed using the
[ngx_http_xslt_module](https://nginx.org/en/docs/http/ngx_http_xslt_module.html) module.

## `autoindex_localtime`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location`

For the HTML [format](https://nginx.org/en/docs/http/ngx_http_autoindex_module.html#autoindex_format),
specifies whether times in the directory listing should be
output in the local time zone or UTC.

