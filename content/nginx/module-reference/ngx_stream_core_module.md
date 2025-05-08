---
title: "ngx_stream_core_module"
id: "/en/docs/stream/ngx_stream_core_module.html"
toc: true
---

## `listen`

**Syntax:** *`address`*:*`port`* [`default_server`] [`ssl`] [`udp`] [`proxy_protocol`] [`setfib`=*`number`*] [`fastopen`=*`number`*] [`backlog`=*`number`*] [`rcvbuf`=*`size`*] [`sndbuf`=*`size`*] [`accept_filter`=*`filter`*] [`deferred`] [`bind`] [`ipv6only`=`on`|`off`] [`reuseport`] [`so_keepalive`=`on`|`off`|[*`keepidle`*]:[*`keepintvl`*]:[*`keepcnt`*]]

**Contexts:** `server`

Sets the *`address`* and *`port`* for the socket
on which the server will accept connections.
It is possible to specify just the port.
The address can also be a hostname, for example:
```
listen 127.0.0.1:12345;
listen *:12345;
listen 12345;     # same as *:12345
listen localhost:12345;
```
IPv6 addresses are specified in square brackets:
```
listen [::1]:12345;
listen [::]:12345;
```
UNIX-domain sockets are specified with the “`unix:`”
prefix:
```
listen unix:/var/run/nginx.sock;
```

Port ranges (1.15.10) are specified with the
first and last port separated by a hyphen:
```
listen 127.0.0.1:12345-12399;
listen 12345-12399;
```

The `default_server` parameter, if present,
will cause the server to become the default server for the specified
*`address`*:*`port`* pair (1.25.5).
If none of the directives have the `default_server`
parameter then the first server with the
*`address`*:*`port`* pair will be
the default server for this pair.

The `ssl` parameter allows specifying that all
connections accepted on this port should work in SSL mode.

The `udp` parameter configures a listening socket
for working with datagrams (1.9.13).
In order to handle packets from the same address and port in the same session,
the [`reuseport`](https://nginx.org/en/docs/stream/ngx_stream_core_module.html#reuseport) parameter
should also be specified.

The `proxy_protocol` parameter (1.11.4)
allows specifying that all connections accepted on this port should use the
[PROXY protocol](http://www.haproxy.org/download/1.8/doc/proxy-protocol.txt).
> The PROXY protocol version 2 is supported since version 1.13.11.

The `listen` directive
can have several additional parameters specific to socket-related system calls.
These parameters can be specified in any
`listen` directive, but only once for a given
*`address`*:*`port`* pair.
- `setfib`=*`number`*

    this parameter (1.25.5) sets the associated routing table, FIB
    (the `SO_SETFIB` option) for the listening socket.
    This currently works only on FreeBSD.
- `fastopen`=*`number`*

    enables
    “[TCP Fast Open](http://en.wikipedia.org/wiki/TCP_Fast_Open)”
    for the listening socket (1.21.0) and
    [limits](https://datatracker.ietf.org/doc/html/rfc7413#section-5.1)
    the maximum length for the queue of connections that have not yet completed
    the three-way handshake.
    > Do not enable this feature unless the server can handle
    > receiving the
    > [ same SYN packet with data](https://datatracker.ietf.org/doc/html/rfc7413#section-6.1) more than once.
- `backlog`=*`number`*

    sets the `backlog` parameter in the
    `listen()` call that limits
    the maximum length for the queue of pending connections (1.9.2).
    By default,
    `backlog` is set to -1 on FreeBSD, DragonFly BSD, and macOS,
    and to 511 on other platforms.
- `rcvbuf`=*`size`*

    sets the receive buffer size
    (the `SO_RCVBUF` option) for the listening socket (1.11.13).
- `sndbuf`=*`size`*

    sets the send buffer size
    (the `SO_SNDBUF` option) for the listening socket (1.11.13).
- `accept_filter`=*`filter`*

    sets the name of accept filter
    (the `SO_ACCEPTFILTER` option) for the listening socket
    that filters incoming connections before passing them to
    `accept()` (1.25.5).
    This works only on FreeBSD and NetBSD 5.0+.
    Possible values are
    [dataready](http://man.freebsd.org/accf_data)
    and
    [httpready](http://man.freebsd.org/accf_http).
- `deferred`

    instructs to use a deferred `accept()`
    (the `TCP_DEFER_ACCEPT` socket option) on Linux (1.25.5).
- `bind`

    this parameter instructs to make a separate `bind()`
    call for a given address:port pair.
    The fact is that if there are several `listen` directives with
    the same port but different addresses, and one of the
    `listen` directives listens on all addresses
    for the given port (`*:`*`port`*), nginx will
    `bind()` only to `*:`*`port`*.
    It should be noted that the `getsockname()` system call will be
    made in this case to determine the address that accepted the connection.
    If the `setfib`,
    `fastopen`,
    `backlog`, `rcvbuf`,
    `sndbuf`, `accept_filter`,
    `deferred`, `ipv6only`,
    `reuseport`,
    or `so_keepalive` parameters
    are used then for a given
    *`address`*:*`port`* pair
    a separate `bind()` call will always be made.
- `ipv6only`=`on`|`off`

    this parameter determines
    (via the `IPV6_V6ONLY` socket option)
    whether an IPv6 socket listening on a wildcard address `[::]`
    will accept only IPv6 connections or both IPv6 and IPv4 connections.
    This parameter is turned on by default.
    It can only be set once on start.
- `reuseport`

    this parameter (1.9.1) instructs to create an individual listening socket
    for each worker process
    (using the
    `SO_REUSEPORT` socket option on Linux 3.9+ and DragonFly BSD,
    or `SO_REUSEPORT_LB` on FreeBSD 12+), allowing a kernel
    to distribute incoming connections between worker processes.
    This currently works only on Linux 3.9+, DragonFly BSD,
    and FreeBSD 12+ (1.15.1).
    > Inappropriate use of this option may have its security
    > [implications](http://man7.org/linux/man-pages/man7/socket.7.html).
- `so_keepalive`=`on`|`off`|[*`keepidle`*]:[*`keepintvl`*]:[*`keepcnt`*]

    this parameter configures the “TCP keepalive” behavior
    for the listening socket.
    If this parameter is omitted then the operating system’s settings will be
    in effect for the socket.
    If it is set to the value “`on`”, the
    `SO_KEEPALIVE` option is turned on for the socket.
    If it is set to the value “`off`”, the
    `SO_KEEPALIVE` option is turned off for the socket.
    Some operating systems support setting of TCP keepalive parameters on
    a per-socket basis using the `TCP_KEEPIDLE`,
    `TCP_KEEPINTVL`, and `TCP_KEEPCNT` socket options.
    On such systems (currently, Linux 2.4+, NetBSD 5+, and
    FreeBSD 9.0-STABLE), they can be configured
    using the *`keepidle`*, *`keepintvl`*, and
    *`keepcnt`* parameters.
    One or two parameters may be omitted, in which case the system default setting
    for the corresponding socket option will be in effect.
    For example,
    ```
    so_keepalive=30m::10
    ```
    will set the idle timeout (`TCP_KEEPIDLE`) to 30 minutes,
    leave the probe interval (`TCP_KEEPINTVL`) at its system default,
    and set the probes count (`TCP_KEEPCNT`) to 10 probes.

> Before version 1.25.5, different servers must listen on different
> *`address`*:*`port`* pairs.

## `preread_buffer_size`

**Syntax:** *`size`*

**Default:** 16k

**Contexts:** `stream, server`

Specifies a *`size`* of the
[preread](https://nginx.org/en/docs/stream/stream_processing.html#preread_phase) buffer.

## `preread_timeout`

**Syntax:** *`timeout`*

**Default:** 30s

**Contexts:** `stream, server`

Specifies a *`timeout`* of the
[preread](https://nginx.org/en/docs/stream/stream_processing.html#preread_phase) phase.

## `proxy_protocol_timeout`

**Syntax:** *`timeout`*

**Default:** 30s

**Contexts:** `stream, server`

Specifies a *`timeout`* for
reading the PROXY protocol header to complete.
If no entire header is transmitted within this time,
the connection is closed.

## `resolver`

**Syntax:** *`address`* ... [`valid`=*`time`*] [`ipv4`=`on`|`off`] [`ipv6`=`on`|`off`] [`status_zone`=*`zone`*]

**Contexts:** `stream, server`

Configures name servers used to resolve names of upstream servers
into addresses, for example:
```
resolver 127.0.0.1 [::1]:5353;
```
The address can be specified as a domain name or IP address,
with an optional port.
If port is not specified, the port 53 is used.
Name servers are queried in a round-robin fashion.

By default, nginx will look up both IPv4 and IPv6 addresses while resolving.
If looking up of IPv4 or IPv6 addresses is not desired,
the `ipv4=off` (1.23.1) or
the `ipv6=off` parameter can be specified.

By default, nginx caches answers using the TTL value of a response.
The optional `valid` parameter allows overriding it:
```
resolver 127.0.0.1 [::1]:5353 valid=30s;
```
> To prevent DNS spoofing, it is recommended
> configuring DNS servers in a properly secured trusted local network.

The optional `status_zone` parameter (1.17.1)
enables
[collection](https://nginx.org/en/docs/http/ngx_http_api_module.html#resolvers_)
of DNS server statistics of requests and responses
in the specified *`zone`*.
The parameter is available as part of our
[commercial subscription](https://nginx.com/products/).

> Before version 1.11.3, this directive was available as part of our
> [commercial subscription](https://nginx.com/products/).

## `resolver_timeout`

**Syntax:** *`time`*

**Default:** 30s

**Contexts:** `stream, server`

Sets a timeout for name resolution, for example:
```
resolver_timeout 5s;
```
> Before version 1.11.3, this directive was available as part of our
> [commercial subscription](https://nginx.com/products/).

## `server`

**Syntax:**  `{...}`

**Contexts:** `stream`

Sets the configuration for a virtual server.
There is no clear separation between IP-based (based on the IP address)
and name-based (based on the
[TLS Server Name Indication extension](http://en.wikipedia.org/wiki/Server_Name_Indication) (SNI, RFC 6066)) (1.25.5)
virtual servers.
Instead, the [`listen`](https://nginx.org/en/docs/stream/ngx_stream_core_module.html#listen) directives describe all
addresses and ports that should accept connections for the server, and the
[`server_name`](https://nginx.org/en/docs/stream/ngx_stream_core_module.html#server_name) directive lists all server names.

## `server_name`

**Syntax:** *`name`* ...

**Default:** ""

**Contexts:** `server`

Sets names of a virtual server, for example:
```
server {
    server_name example.com www.example.com;
}
```

The first name becomes the primary server name.

Server names can include an asterisk (“`*`”)
replacing the first or last part of a name:
```
server {
    server_name example.com *.example.com www.example.*;
}
```
Such names are called wildcard names.

The first two of the names mentioned above can be combined in one:
```
server {
    server_name .example.com;
}
```

It is also possible to use regular expressions in server names,
preceding the name with a tilde (“`~`”):
```
server {
    server_name www.example.com ~^www\d+\.example\.com$;
}
```

Regular expressions can contain captures that can later
be used in other directives:
```
server {
    server_name ~^(www\.)?(.+)$;

    proxy_pass www.$2:12345;
}
```

Named captures in regular expressions create variables
that can later be used in other directives:
```
server {
    server_name ~^(www\.)?(?<domain>.+)$;

    proxy_pass www.$domain:12345;
}
```

If the directive’s parameter is set to “`$hostname`”, the
machine’s hostname is inserted.

During searching for a virtual server by name,
if the name matches more than one of the specified variants,
(e.g. both a wildcard name and regular expression match), the first matching
variant will be chosen, in the following order of priority:
1. the exact name
2. the longest wildcard name starting with an asterisk,
    e.g. “`*.example.com`”
3. the longest wildcard name ending with an asterisk,
    e.g. “`mail.*`”
4. the first matching regular expression
    (in order of appearance in the configuration file)

## `server_names_hash_bucket_size`

**Syntax:** *`size`*

**Default:** 32|64|128

**Contexts:** `stream`

Sets the bucket size for the server names hash tables.
The default value depends on the size of the processor’s cache line.
The details of setting up hash tables are provided in a separate
[document](https://nginx.org/en/docs/hash.html).

## `server_names_hash_max_size`

**Syntax:** *`size`*

**Default:** 512

**Contexts:** `stream`

Sets the maximum *`size`* of the server names hash tables.
The details of setting up hash tables are provided in a separate
[document](https://nginx.org/en/docs/hash.html).

## `stream`

**Syntax:**  `{...}`

**Contexts:** `main`

Provides the configuration file context in which the stream server directives
are specified.

## `tcp_nodelay`

**Syntax:** `on` | `off`

**Default:** on

**Contexts:** `stream, server`

Enables or disables the use of the `TCP_NODELAY` option.
The option is enabled for both client and proxied server connections.

## `variables_hash_bucket_size`

**Syntax:** *`size`*

**Default:** 64

**Contexts:** `stream`

Sets the bucket size for the variables hash table.
The details of setting up hash tables are provided in a separate
[document](https://nginx.org/en/docs/hash.html).

## `variables_hash_max_size`

**Syntax:** *`size`*

**Default:** 1024

**Contexts:** `stream`

Sets the maximum *`size`* of the variables hash table.
The details of setting up hash tables are provided in a separate
[document](https://nginx.org/en/docs/hash.html).

