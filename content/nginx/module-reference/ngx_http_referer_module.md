---
title: "ngx_http_referer_module"
id: "/en/docs/http/ngx_http_referer_module.html"
toc: true
---

## `referer_hash_bucket_size`

**Syntax:** *`size`*

**Default:** 64

**Contexts:** `server, location`

Sets the bucket size for the valid referers hash tables.
The details of setting up hash tables are provided in a separate
[document](https://nginx.org/en/docs/hash.html).

## `referer_hash_max_size`

**Syntax:** *`size`*

**Default:** 2048

**Contexts:** `server, location`

Sets the maximum *`size`* of the valid referers hash tables.
The details of setting up hash tables are provided in a separate
[document](https://nginx.org/en/docs/hash.html).

## `valid_referers`

**Syntax:** `none` | `blocked` | `server_names` | *`string`* ...

**Contexts:** `server, location`

Specifies the "Referer" request header field values
that will cause the embedded `$invalid_referer` variable to
be set to an empty string.
Otherwise, the variable will be set to “`1`”.
Search for a match is case-insensitive.

Parameters can be as follows:
- `none`

    the "Referer" field is missing in the request header;
- `blocked`

    the "Referer" field is present in the request header,
    but its value has been deleted by a firewall or proxy server;
    such values are strings that do not start with
    “`http://`” or “`https://`”;
- `server_names`

    the "Referer" request header field contains
    one of the server names;
- arbitrary string

    defines a server name and an optional URI prefix.
    A server name can have an “`*`” at the beginning or end.
    During the checking, the server’s port in the "Referer" field
    is ignored;
- regular expression

    the first symbol should be a “`~`”.
    It should be noted that an expression will be matched against
    the text starting after the “`http://`”
    or “`https://`”.

Example:
```
valid_referers none blocked server_names
               *.example.com example.* www.example.org/galleries/
               ~\.google\.;
```

