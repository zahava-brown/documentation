---
title: "ngx_http_memcached_module"
id: "/en/docs/http/ngx_http_memcached_module.html"
toc: true
---

## `memcached_bind`

**Syntax:** *`address`* [`transparent `] | `off`

**Contexts:** `http, server, location`

Makes outgoing connections to a memcached server originate
from the specified local IP address with an optional port (1.11.2).
Parameter value can contain variables (1.3.12).
The special value `off` (1.3.12) cancels the effect
of the `memcached_bind` directive
inherited from the previous configuration level, which allows the
system to auto-assign the local IP address and port.

The `transparent` parameter (1.11.0) allows
outgoing connections to a memcached server originate
from a non-local IP address,
for example, from a real IP address of a client:
```
memcached_bind $remote_addr transparent;
```
In order for this parameter to work,
it is usually necessary to run nginx worker processes with the
[superuser](https://nginx.org/en/docs/ngx_core_module.html#user) privileges.
On Linux it is not required (1.13.8) as if
the `transparent` parameter is specified, worker processes
inherit the `CAP_NET_RAW` capability from the master process.
It is also necessary to configure kernel routing table
to intercept network traffic from the memcached server.

## `memcached_buffer_size`

**Syntax:** *`size`*

**Default:** 4k|8k

**Contexts:** `http, server, location`

Sets the *`size`* of the buffer used for reading the response
received from the memcached server.
The response is passed to the client synchronously, as soon as it is received.

## `memcached_connect_timeout`

**Syntax:** *`time`*

**Default:** 60s

**Contexts:** `http, server, location`

Defines a timeout for establishing a connection with a memcached server.
It should be noted that this timeout cannot usually exceed 75 seconds.

## `memcached_gzip_flag`

**Syntax:** *`flag`*

**Contexts:** `http, server, location`

Enables the test for the *`flag`* presence in the memcached
server response and sets the “`Content-Encoding`”
response header field to “`gzip`”
if the flag is set.

## `memcached_next_upstream`

**Syntax:** `error` | `timeout` | `invalid_response` | `not_found` | `off` ...

**Default:** error timeout

**Contexts:** `http, server, location`

Specifies in which cases a request should be passed to the next server:
- `error`

    an error occurred while establishing a connection with the
    server, passing a request to it, or reading the response header;
- `timeout`

    a timeout has occurred while establishing a connection with the
    server, passing a request to it, or reading the response header;
- `invalid_response`

    a server returned an empty or invalid response;
- `not_found`

    a response was not found on the server;
- `off`

    disables passing a request to the next server.

One should bear in mind that passing a request to the next server is
only possible if nothing has been sent to a client yet.
That is, if an error or timeout occurs in the middle of the
transferring of a response, fixing this is impossible.

The directive also defines what is considered an
[unsuccessful attempt](https://nginx.org/en/docs/http/ngx_http_upstream_module.html#max_fails) of communication with a server.
The cases of `error`, `timeout` and
`invalid_response` are always considered unsuccessful attempts,
even if they are not specified in the directive.
The case of `not_found`
is never considered an unsuccessful attempt.

Passing a request to the next server can be limited by
[the number of tries](https://nginx.org/en/docs/http/ngx_http_memcached_module.html#memcached_next_upstream_tries)
and by [time](https://nginx.org/en/docs/http/ngx_http_memcached_module.html#memcached_next_upstream_timeout).

## `memcached_next_upstream_timeout`

**Syntax:** *`time`*

**Default:** 0

**Contexts:** `http, server, location`

Limits the time during which a request can be passed to the
[next server](https://nginx.org/en/docs/http/ngx_http_memcached_module.html#memcached_next_upstream).
The `0` value turns off this limitation.

## `memcached_next_upstream_tries`

**Syntax:** *`number`*

**Default:** 0

**Contexts:** `http, server, location`

Limits the number of possible tries for passing a request to the
[next server](https://nginx.org/en/docs/http/ngx_http_memcached_module.html#memcached_next_upstream).
The `0` value turns off this limitation.

## `memcached_pass`

**Syntax:** *`address`*

**Contexts:** `location, if in location`

Sets the memcached server address.
The address can be specified as a domain name or IP address,
and a port:
```
memcached_pass localhost:11211;
```
or as a UNIX-domain socket path:
```
memcached_pass unix:/tmp/memcached.socket;
```

If a domain name resolves to several addresses, all of them will be
used in a round-robin fashion.
In addition, an address can be specified as a
[server group](https://nginx.org/en/docs/http/ngx_http_upstream_module.html).

## `memcached_read_timeout`

**Syntax:** *`time`*

**Default:** 60s

**Contexts:** `http, server, location`

Defines a timeout for reading a response from the memcached server.
The timeout is set only between two successive read operations,
not for the transmission of the whole response.
If the memcached server does not transmit anything within this time,
the connection is closed.

## `memcached_send_timeout`

**Syntax:** *`time`*

**Default:** 60s

**Contexts:** `http, server, location`

Sets a timeout for transmitting a request to the memcached server.
The timeout is set only between two successive write operations,
not for the transmission of the whole request.
If the memcached server does not receive anything within this time,
the connection is closed.

## `memcached_socket_keepalive`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location`

Configures the “TCP keepalive” behavior
for outgoing connections to a memcached server.
By default, the operating system’s settings are in effect for the socket.
If the directive is set to the value “`on`”, the
`SO_KEEPALIVE` socket option is turned on for the socket.

