---
title: "ngx_http_userid_module"
id: "/en/docs/http/ngx_http_userid_module.html"
toc: true
---

## `userid`

**Syntax:** `on` | `v1` | `log` | `off`

**Default:** off

**Contexts:** `http, server, location`

Enables or disables setting cookies and logging the received cookies:
- `on`

    enables the setting of version 2 cookies
    and logging of the received cookies;
- `v1`

    enables the setting of version 1 cookies
    and logging of the received cookies;
- `log`

    disables the setting of cookies,
    but enables logging of the received cookies;
- `off`

    disables the setting of cookies and logging of the received cookies.

## `userid_domain`

**Syntax:** *`name`* | `none`

**Default:** none

**Contexts:** `http, server, location`

Defines a domain for which the cookie is set.
The `none` parameter disables setting of a domain for the
cookie.

## `userid_expires`

**Syntax:** *`time`* | `max` | `off`

**Default:** off

**Contexts:** `http, server, location`

Sets a time during which a browser should keep the cookie.
The parameter `max` will cause the cookie to expire on
“`31 Dec 2037 23:55:55 GMT`”.
The parameter `off` will cause the cookie to expire at
the end of a browser session.

## `userid_flags`

**Syntax:** `off` | *`flag`* ...

**Default:** off

**Contexts:** `http, server, location`

If the parameter is not `off`,
defines one or more additional flags for the cookie:
`secure`,
`httponly`,
`samesite=strict`,
`samesite=lax`,
`samesite=none`.

## `userid_mark`

**Syntax:** *`letter`* | *`digit`* | `=` | `off`

**Default:** off

**Contexts:** `http, server, location`

If the parameter is not `off`, enables the cookie marking
mechanism and sets the character used as a mark.
This mechanism is used to add or change
[`userid_p3p`](https://nginx.org/en/docs/http/ngx_http_userid_module.html#userid_p3p) and/or a cookie expiration time while
preserving the client identifier.
A mark can be any letter of the English alphabet (case-sensitive),
digit, or the “`=`” character.

If the mark is set, it is compared with the first padding symbol
in the base64 representation of the client identifier passed in a cookie.
If they do not match, the cookie is resent with the specified mark,
expiration time, and "P3P" header.

## `userid_name`

**Syntax:** *`name`*

**Default:** uid

**Contexts:** `http, server, location`

Sets the cookie name.

## `userid_p3p`

**Syntax:** *`string`* | `none`

**Default:** none

**Contexts:** `http, server, location`

Sets a value for the "P3P" header field that will be
sent along with the cookie.
If the directive is set to the special value `none`,
the "P3P" header will not be sent in a response.

## `userid_path`

**Syntax:** *`path`*

**Default:** /

**Contexts:** `http, server, location`

Defines a path for which the cookie is set.

## `userid_service`

**Syntax:** *`number`*

**Default:** IP address of the server

**Contexts:** `http, server, location`

If identifiers are issued by multiple servers (services),
each service should be assigned its own *`number`*
to ensure that client identifiers are unique.
For version 1 cookies, the default value is zero.
For version 2 cookies, the default value is the number composed from the last
four octets of the server’s IP address.

