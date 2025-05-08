---
title: "ngx_http_browser_module"
id: "/en/docs/http/ngx_http_browser_module.html"
toc: true
---

## `ancient_browser`

**Syntax:** *`string`* ...

**Contexts:** `http, server, location`

If any of the specified substrings is found in the "User-Agent"
request header field, the browser will be considered ancient.
The special string “`netscape4`” corresponds to the
regular expression “`^Mozilla/[1-4]`”.

## `ancient_browser_value`

**Syntax:** *`string`*

**Default:** 1

**Contexts:** `http, server, location`

Sets a value for the `$ancient_browser` variables.

## `modern_browser`

**Syntax:** *`browser`* *`version`*

**Contexts:** `http, server, location`

Specifies a version starting from which a browser is considered modern.
A browser can be any one of the following: `msie`,
`gecko` (browsers based on Mozilla),
`opera`, `safari`,
or `konqueror`.

Versions can be specified in the following formats: X, X.X, X.X.X, or X.X.X.X.
The maximum values for each of the format are
4000, 4000.99, 4000.99.99, and 4000.99.99.99, respectively.

The special value `unlisted` specifies to consider
a browser as modern if it was not listed by the
`modern_browser` and [`ancient_browser`](https://nginx.org/en/docs/http/ngx_http_browser_module.html#ancient_browser)
directives.
Otherwise such a browser is considered ancient.
If a request does not provide the "User-Agent" field
in the header, the browser is treated as not being listed.

## `modern_browser_value`

**Syntax:** *`string`*

**Default:** 1

**Contexts:** `http, server, location`

Sets a value for the `$modern_browser` variables.

