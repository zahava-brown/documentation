---
title: "ngx_http_v2_module"
id: "/en/docs/http/ngx_http_v2_module.html"
toc: true
---

## `http2`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server`

Enables
the [HTTP/2](https://datatracker.ietf.org/doc/html/rfc9113)
protocol.

## `http2_body_preread_size`

**Syntax:** *`size`*

**Default:** 64k

**Contexts:** `http, server`

Sets the *`size`* of the buffer per each request
in which the request body may be saved
before it is started to be processed.

## `http2_chunk_size`

**Syntax:** *`size`*

**Default:** 8k

**Contexts:** `http, server, location`

Sets the maximum size of chunks
into which the response body is sliced.
A too low value results in higher overhead.
A too high value impairs prioritization due to
[ HOL blocking](http://en.wikipedia.org/wiki/Head-of-line_blocking).

## `http2_idle_timeout`

**Syntax:** *`time`*

**Default:** 3m

**Contexts:** `http, server`

> This directive is obsolete since version 1.19.7.
> The [`keepalive_timeout`](https://nginx.org/en/docs/http/ngx_http_core_module.html#keepalive_timeout)
> directive should be used instead.

Sets the timeout of inactivity after which the connection is closed.

## `http2_max_concurrent_pushes`

**Syntax:** *`number`*

**Default:** 10

**Contexts:** `http, server`

> This directive is obsolete since version 1.25.1.

Limits the maximum number of concurrent
[push](https://nginx.org/en/docs/http/ngx_http_v2_module.html#http2_push) requests in a connection.

## `http2_max_concurrent_streams`

**Syntax:** *`number`*

**Default:** 128

**Contexts:** `http, server`

Sets the maximum number of concurrent HTTP/2 streams
in a connection.

## `http2_max_field_size`

**Syntax:** *`size`*

**Default:** 4k

**Contexts:** `http, server`

> This directive is obsolete since version 1.19.7.
> The [`large_client_header_buffers`](https://nginx.org/en/docs/http/ngx_http_core_module.html#large_client_header_buffers)
> directive should be used instead.

Limits the maximum size of
an [HPACK](https://datatracker.ietf.org/doc/html/rfc7541)-compressed
request header field.
The limit applies equally to both name and value.
Note that if Huffman encoding is applied,
the actual size of decompressed name and value strings may be larger.
For most requests, the default limit should be enough.

## `http2_max_header_size`

**Syntax:** *`size`*

**Default:** 16k

**Contexts:** `http, server`

> This directive is obsolete since version 1.19.7.
> The [`large_client_header_buffers`](https://nginx.org/en/docs/http/ngx_http_core_module.html#large_client_header_buffers)
> directive should be used instead.

Limits the maximum size of the entire request header list after
[HPACK](https://datatracker.ietf.org/doc/html/rfc7541) decompression.
For most requests, the default limit should be enough.

## `http2_max_requests`

**Syntax:** *`number`*

**Default:** 1000

**Contexts:** `http, server`

> This directive is obsolete since version 1.19.7.
> The [`keepalive_requests`](https://nginx.org/en/docs/http/ngx_http_core_module.html#keepalive_requests)
> directive should be used instead.

Sets the maximum number of requests (including
[push](https://nginx.org/en/docs/http/ngx_http_v2_module.html#http2_push) requests) that can be served
through one HTTP/2 connection,
after which the next client request will lead to connection closing
and the need of establishing a new connection.

Closing connections periodically is necessary to free
per-connection memory allocations.
Therefore, using too high maximum number of requests
could result in excessive memory usage and not recommended.

## `http2_push`

**Syntax:** *`uri`* | `off`

**Default:** off

**Contexts:** `http, server, location`

> This directive is obsolete since version 1.25.1.

Pre-emptively sends
([pushes](https://datatracker.ietf.org/doc/html/rfc9113#section-8.4))
a request to the specified *`uri`*
along with the response to the original request.
Only relative URIs with absolute path will be processed,
for example:
```
http2_push /static/css/main.css;
```
The *`uri`* value can contain variables.

Several `http2_push` directives
can be specified on the same configuration level.
The `off` parameter cancels the effect
of the `http2_push` directives
inherited from the previous configuration level.

## `http2_push_preload`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location`

> This directive is obsolete since version 1.25.1.

Enables automatic conversion of
[preload links](https://www.w3.org/TR/preload/#server-push-http-2)
specified in the "Link" response header fields into
[push](https://datatracker.ietf.org/doc/html/rfc9113#section-8.4)
requests.

## `http2_recv_buffer_size`

**Syntax:** *`size`*

**Default:** 256k

**Contexts:** `http`

Sets the size of the per
[worker](https://nginx.org/en/docs/ngx_core_module.html#worker_processes)
input buffer.

## `http2_recv_timeout`

**Syntax:** *`time`*

**Default:** 30s

**Contexts:** `http, server`

> This directive is obsolete since version 1.19.7.
> The [`client_header_timeout`](https://nginx.org/en/docs/http/ngx_http_core_module.html#client_header_timeout)
> directive should be used instead.

Sets the timeout for expecting more data from the client,
after which the connection is closed.

