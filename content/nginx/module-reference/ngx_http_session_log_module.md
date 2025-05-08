---
title: "ngx_http_session_log_module"
id: "/en/docs/http/ngx_http_session_log_module.html"
toc: true
---

## `session_log`

**Syntax:** *`name`* | `off`

**Default:** off

**Contexts:** `http, server, location`

Enables the use of the specified session log.
The special value `off` cancels the effect
of the `session_log` directives
inherited from the previous configuration level.

## `session_log_format`

**Syntax:** *`name`* *`string`* ...

**Default:** combined "..."

**Contexts:** `http`

Specifies the output format of a log.
The value of the `$body_bytes_sent` variable is aggregated across
all requests in a session.
The values of all other variables available for logging correspond to the
first request in a session.

## `session_log_zone`

**Syntax:** *`path`* `zone`=*`name`*:*`size`* [`format`=*`format`*] [`timeout`=*`time`*] [`id`=*`id`*] [`md5`=*`md5`*]

**Contexts:** `http`

Sets the path to a log file and configures the shared memory zone that is used
to store currently active sessions.

A session is considered active for as long as the time elapsed since
the last request in the session does not exceed the specified
`timeout` (by default, 30 seconds).
Once a session is no longer active, it is written to the log.

The `id` parameter identifies the
session to which a request is mapped.
The `id` parameter is set to the hexadecimal representation
of an MD5 hash (for example, obtained from a cookie using variables).
If this parameter is not specified or does not represent the valid
MD5 hash, nginx computes the MD5 hash from the value of
the `md5` parameter and creates a new session using this hash.
Both the `id` and `md5` parameters
can contain variables.

The `format` parameter sets the custom session log
format configured by the [`session_log_format`](https://nginx.org/en/docs/http/ngx_http_session_log_module.html#session_log_format) directive.
If `format` is not specified, the predefined
“`combined`” format is used.

