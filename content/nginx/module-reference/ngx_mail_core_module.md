---
title: "ngx_mail_core_module"
id: "/en/docs/mail/ngx_mail_core_module.html"
toc: true
---

## `listen`

**Syntax:** *`address`*:*`port`* [`ssl`] [`proxy_protocol`] [`backlog`=*`number`*] [`rcvbuf`=*`size`*] [`sndbuf`=*`size`*] [`bind`] [`ipv6only`=`on`|`off`] [`so_keepalive`=`on`|`off`|[*`keepidle`*]:[*`keepintvl`*]:[*`keepcnt`*]]

**Contexts:** `server`

Sets the *`address`* and *`port`* for the socket
on which the server will accept requests.
It is possible to specify just the port.
The address can also be a hostname, for example:
```
listen 127.0.0.1:110;
listen *:110;
listen 110;     # same as *:110
listen localhost:110;
```
IPv6 addresses (0.7.58) are specified in square brackets:
```
listen [::1]:110;
listen [::]:110;
```
UNIX-domain sockets (1.3.5) are specified with the “`unix:`”
prefix:
```
listen unix:/var/run/nginx.sock;
```

Different servers must listen on different
*`address`*:*`port`* pairs.

The `ssl` parameter allows specifying that all
connections accepted on this port should work in SSL mode.

The `proxy_protocol` parameter (1.19.8)
allows specifying that all connections accepted on this port should use the
[PROXY protocol](http://www.haproxy.org/download/1.8/doc/proxy-protocol.txt).
Obtained information is passed to the
[authentication server](https://nginx.org/en/docs/mail/ngx_mail_auth_http_module.html#proxy_protocol)
and can be used to
[change the client address](https://nginx.org/en/docs/mail/ngx_mail_realip_module.html).

The `listen` directive
can have several additional parameters specific to socket-related system calls.
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
    If the `backlog`,
    `rcvbuf`, `sndbuf`,
    `ipv6only`,
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

## `mail`

**Syntax:**  `{...}`

**Contexts:** `main`

Provides the configuration file context in which the mail server directives
are specified.

## `max_errors`

**Syntax:** *`number`*

**Default:** 5

**Contexts:** `mail, server`

Sets the number of protocol errors after which the connection is closed.

## `protocol`

**Syntax:** `imap` | `pop3` | `smtp`

**Contexts:** `server`

Sets the protocol for a proxied server.
Supported protocols are
[IMAP](https://nginx.org/en/docs/mail/ngx_mail_imap_module.html),
[POP3](https://nginx.org/en/docs/mail/ngx_mail_pop3_module.html), and
[SMTP](https://nginx.org/en/docs/mail/ngx_mail_smtp_module.html).

If the directive is not set, the protocol can be detected automatically
based on the well-known port specified in the [`listen`](https://nginx.org/en/docs/mail/ngx_mail_core_module.html#listen)
directive:
- `imap`: 143, 993
- `pop3`: 110, 995
- `smtp`: 25, 587, 465

Unnecessary protocols can be disabled using the
[configuration](https://nginx.org/en/docs/configure.html)
parameters `--without-mail_imap_module`,
`--without-mail_pop3_module`, and
`--without-mail_smtp_module`.

## `resolver`

**Syntax:** *`address`* ... [`valid`=*`time`*] [`ipv4`=`on`|`off`] [`ipv6`=`on`|`off`] [`status_zone`=*`zone`*]

**Default:** off

**Contexts:** `mail, server`

Configures name servers used to find the client’s hostname
to pass it to the
[authentication server](https://nginx.org/en/docs/mail/ngx_mail_auth_http_module.html),
and in the
[XCLIENT](https://nginx.org/en/docs/mail/ngx_mail_proxy_module.html#xclient)
command when proxying SMTP.
For example:
```
resolver 127.0.0.1 [::1]:5353;
```
The address can be specified as a domain name or IP address,
with an optional port (1.3.1, 1.2.2).
If port is not specified, the port 53 is used.
Name servers are queried in a round-robin fashion.
> Before version 1.1.7, only a single name server could be configured.
> Specifying name servers using IPv6 addresses is supported
> starting from versions 1.3.1 and 1.2.2.

By default, nginx will look up both IPv4 and IPv6 addresses while resolving.
If looking up of IPv4 or IPv6 addresses is not desired,
the `ipv4=off` (1.23.1) or
the `ipv6=off` parameter can be specified.
> Resolving of names into IPv6 addresses is supported
> starting from version 1.5.8.

By default, nginx caches answers using the TTL value of a response.
An optional `valid` parameter allows overriding it:
```
resolver 127.0.0.1 [::1]:5353 valid=30s;
```
> Before version 1.1.9, tuning of caching time was not possible,
> and nginx always cached answers for the duration of 5 minutes.

> To prevent DNS spoofing, it is recommended
> configuring DNS servers in a properly secured trusted local network.

The optional `status_zone` parameter (1.17.1)
enables
[collection](https://nginx.org/en/docs/http/ngx_http_api_module.html#resolvers_)
of DNS server statistics of requests and responses
in the specified *`zone`*.
The parameter is available as part of our
[commercial subscription](https://nginx.com/products/).

The special value `off` disables resolving.

## `resolver_timeout`

**Syntax:** *`time`*

**Default:** 30s

**Contexts:** `mail, server`

Sets a timeout for DNS operations, for example:
```
resolver_timeout 5s;
```

## `server`

**Syntax:**  `{...}`

**Contexts:** `mail`

Sets the configuration for a server.

## `server_name`

**Syntax:** *`name`*

**Default:** hostname

**Contexts:** `mail, server`

Sets the server name that is used:
- in the initial POP3/SMTP server greeting;
- in the salt during the SASL CRAM-MD5 authentication;
- in the `EHLO` command when connecting to the SMTP backend,
    if the passing of the
    [XCLIENT](https://nginx.org/en/docs/mail/ngx_mail_proxy_module.html#xclient) command
    is enabled.

If the directive is not specified, the machine’s hostname is used.

## `timeout`

**Syntax:** *`time`*

**Default:** 60s

**Contexts:** `mail, server`

Sets the timeout that is used before proxying to the backend starts.

