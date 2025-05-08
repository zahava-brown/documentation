---
title: "ngx_stream_upstream_module"
id: "/en/docs/stream/ngx_stream_upstream_module.html"
toc: true
---

## `upstream`

**Syntax:** *`name`* `{...}`

**Contexts:** `stream`

Defines a group of servers.
Servers can listen on different ports.
In addition, servers listening on TCP and UNIX-domain sockets
can be mixed.

Example:
```
upstream backend {
    server backend1.example.com:12345 weight=5;
    server 127.0.0.1:12345            max_fails=3 fail_timeout=30s;
    server unix:/tmp/backend2;
    server backend3.example.com:12345 resolve;

    server backup1.example.com:12345  backup;
}
```

By default, connections are distributed between the servers using a
weighted round-robin balancing method.
In the above example, each 7 connections will be distributed as follows:
5 connections go to `backend1.example.com:12345`
and one connection to each of the second and third servers.
If an error occurs during communication with a server, the connection will
be passed to the next server, and so on until all of the functioning
servers will be tried.
If communication with all servers fails, the connection will be closed.

## `server`

**Syntax:** *`address`* [*`parameters`*]

**Contexts:** `upstream`

Defines the *`address`* and other *`parameters`*
of a server.
The address can be specified as a domain name or IP address
with an obligatory port, or as a UNIX-domain socket path
specified after the “`unix:`” prefix.
A domain name that resolves to several IP addresses defines
multiple servers at once.

The following parameters can be defined:
- `weight`=*`number`*

    sets the weight of the server, by default, 1.
- `max_conns`=*`number`*

    limits the maximum *`number`* of simultaneous
    connections to the proxied server (1.11.5).
    Default value is zero, meaning there is no limit.
    If the server group does not reside in the [shared memory](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#zone),
    the limitation works per each worker process.
    > Prior to version 1.11.5, this parameter was available as part of our
    > [commercial subscription](https://nginx.com/products/).
- `max_fails`=*`number`*

    sets the number of unsuccessful attempts to communicate with the server
    that should happen in the duration set by the `fail_timeout`
    parameter to consider the server unavailable for a duration also set by the
    `fail_timeout` parameter.
    By default, the number of unsuccessful attempts is set to 1.
    The zero value disables the accounting of attempts.
    Here, an unsuccessful attempt is an error or timeout
    while establishing a connection with the server.
- `fail_timeout`=*`time`*

    sets
    - the time during which the specified number of unsuccessful attempts to
        communicate with the server should happen to consider the server unavailable;
    - and the period of time the server will be considered unavailable.
    
    By default, the parameter is set to 10 seconds.
- `backup`

    marks the server as a backup server.
    Connections to the backup server will be passed
    when the primary servers are unavailable.
    > The parameter cannot be used along with the
    > [`hash`](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#hash) and [`random`](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#random) load balancing methods.
- `down`

    marks the server as permanently unavailable.
- `resolve`

    monitors changes of the IP addresses
    that correspond to a domain name of the server,
    and automatically modifies the upstream configuration
    without the need of restarting nginx.
    The server group must reside in the [shared memory](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#zone).
    
    In order for this parameter to work,
    the `resolver` directive
    must be specified in the
    [stream](https://nginx.org/en/docs/stream/ngx_stream_core_module.html#resolver) block
    or in the corresponding [upstream](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#resolver) block.
    
    
    
    > Prior to version 1.27.3, this parameter was available only as part of our
    > [commercial subscription](https://nginx.com/products/).
- `service`=*`name`*

    enables resolving of DNS
    [SRV](https://datatracker.ietf.org/doc/html/rfc2782)
    records and sets the service *`name`* (1.9.13).
    In order for this parameter to work, it is necessary to specify
    the [`resolve`](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#resolve) parameter for the server
    and specify a hostname without a port number.
    
    If the service name does not contain a dot (“`.`”), then
    the [RFC](https://datatracker.ietf.org/doc/html/rfc2782)-compliant name
    is constructed
    and the TCP protocol is added to the service prefix.
    For example, to look up the
    `_http._tcp.backend.example.com` SRV record,
    it is necessary to specify the directive:
    ```
    server backend.example.com service=http resolve;
    ```
    If the service name contains one or more dots, then the name is constructed
    by joining the service prefix and the server name.
    For example, to look up the `_http._tcp.backend.example.com`
    and `server1.backend.example.com` SRV records,
    it is necessary to specify the directives:
    ```
    server backend.example.com service=_http._tcp resolve;
    server example.com service=server1.backend resolve;
    ```
    
    
    
    Highest-priority SRV records
    (records with the same lowest-number priority value)
    are resolved as primary servers,
    the rest of SRV records are resolved as backup servers.
    If the [`backup`](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#backup) parameter is specified for the server,
    high-priority SRV records are resolved as backup servers,
    the rest of SRV records are ignored.
    
    
    
    > Prior to version 1.27.3, this parameter was available only as part of our
    > [commercial subscription](https://nginx.com/products/).

Additionally,
the following parameters are available as part of our
[commercial subscription](https://nginx.com/products/):
- `slow_start`=*`time`*

    sets the *`time`* during which the server will recover its weight
    from zero to a nominal value, when unhealthy server becomes
    [healthy](https://nginx.org/en/docs/stream/ngx_stream_upstream_hc_module.html#health_check),
    or when the server becomes available after a period of time
    it was considered [unavailable](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#fail_timeout).
    Default value is zero, i.e. slow start is disabled.
    > The parameter cannot be used along with the
    > [`hash`](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#hash) and [`random`](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#random) load balancing methods.

> If there is only a single server in a group, `max_fails`,
> `fail_timeout` and `slow_start` parameters
> are ignored, and such a server will never be considered unavailable.

## `zone`

**Syntax:** *`name`* [*`size`*]

**Contexts:** `upstream`

Defines the *`name`* and *`size`* of the shared
memory zone that keeps the group’s configuration and run-time state that are
shared between worker processes.
Several groups may share the same zone.
In this case, it is enough to specify the *`size`* only once.

Additionally,
as part of our [commercial subscription](https://nginx.com/products/),
such groups allow changing the group membership
or modifying the settings of a particular server
without the need of restarting nginx.
The configuration is accessible via the
[API](https://nginx.org/en/docs/http/ngx_http_api_module.html) module (1.13.3).
> Prior to version 1.13.3,
> the configuration was accessible only via a special location
> handled by
> [`upstream_conf`](https://nginx.org/en/docs/http/ngx_http_upstream_conf_module.html#upstream_conf).

## `state`

**Syntax:** *`file`*

**Contexts:** `upstream`

Specifies a *`file`* that keeps the state
of the dynamically configurable group.

Examples:
```
state /var/lib/nginx/state/servers.conf; # path for Linux
state /var/db/nginx/state/servers.conf;  # path for FreeBSD
```

The state is currently limited to the list of servers with their parameters.
The file is read when parsing the configuration and is updated each time
the upstream configuration is
[changed](https://nginx.org/en/docs/http/ngx_http_api_module.html#stream_upstreams_stream_upstream_name_servers_).
Changing the file content directly should be avoided.
The directive cannot be used
along with the [`server`](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#server) directive.

> Changes made during
> [configuration reload](https://nginx.org/en/docs/control.html#reconfiguration)
> or [binary upgrade](https://nginx.org/en/docs/control.html#upgrade)
> can be lost.

> This directive is available as part of our
> [commercial subscription](https://nginx.com/products/).

## `hash`

**Syntax:** *`key`* [`consistent`]

**Contexts:** `upstream`

Specifies a load balancing method for a server group
where the client-server mapping is based on the hashed *`key`* value.
The *`key`* can contain text, variables,
and their combinations (1.11.2).
Usage example:
```
hash $remote_addr;
```
Note that adding or removing a server from the group
may result in remapping most of the keys to different servers.
The method is compatible with the
[Cache::Memcached](https://metacpan.org/pod/Cache::Memcached)
Perl library.

If the `consistent` parameter is specified,
the [ketama](https://www.metabrew.com/article/libketama-consistent-hashing-algo-memcached-clients)
consistent hashing method will be used instead.
The method ensures that only a few keys
will be remapped to different servers
when a server is added to or removed from the group.
This helps to achieve a higher cache hit ratio for caching servers.
The method is compatible with the
[Cache::Memcached::Fast](https://metacpan.org/pod/Cache::Memcached::Fast)
Perl library with the *`ketama_points`* parameter set to 160.

## `least_conn`

**Contexts:** `upstream`

Specifies that a group should use a load balancing method where a connection
is passed to the server with the least number of active connections,
taking into account weights of servers.
If there are several such servers, they are tried in turn using a
weighted round-robin balancing method.

## `least_time`

**Syntax:** `connect` | `first_byte` | `last_byte` [`inflight`]

**Contexts:** `upstream`

Specifies that a group should use a load balancing method where a connection
is passed to the server with the least average time and
least number of active connections, taking into account weights of servers.
If there are several such servers, they are tried in turn using a
weighted round-robin balancing method.

If the `connect` parameter is specified,
time to
[connect](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#var_upstream_connect_time)
to the upstream server is used.
If the `first_byte` parameter is specified,
time to receive the
[first byte](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#var_upstream_first_byte_time) of data is used.
If the `last_byte` is specified,
time to receive the
[last byte](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#var_upstream_session_time) of data is used.
If the `inflight` parameter is specified (1.11.6),
incomplete connections are also taken into account.
> Prior to version 1.11.6,
> incomplete connections were taken into account by default.

> This directive is available as part of our
> [commercial subscription](https://nginx.com/products/).

## `random`

**Syntax:** [`two` [*`method`*]]

**Contexts:** `upstream`

Specifies that a group should use a load balancing method where a connection
is passed to a randomly selected server, taking into account weights
of servers.

The optional `two` parameter
instructs nginx to randomly select
[two](https://homes.cs.washington.edu/~karlin/papers/balls.pdf)
servers and then choose a server
using the specified `method`.
The default method is `least_conn`
which passes a connection to a server
with the least number of active connections.

The `least_time` method passes a connection to a server
with the least average time and least number of active connections.
If `least_time=connect` parameter is specified,
time to
[connect](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#var_upstream_connect_time)
to the upstream server is used.
If `least_time=first_byte` parameter is specified,
time to receive the
[first byte](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#var_upstream_first_byte_time) of data is used.
If `least_time=last_byte` is specified,
time to receive the
[last byte](https://nginx.org/en/docs/stream/ngx_stream_upstream_module.html#var_upstream_session_time) of data is used.
> The `least_time` method is available as a part of our
> [commercial subscription](https://nginx.com/products/).

## `resolver`

**Syntax:** *`address`* ... [`valid`=*`time`*] [`ipv4`=`on`|`off`] [`ipv6`=`on`|`off`] [`status_zone`=*`zone`*]

**Contexts:** `upstream`

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

The optional `status_zone` parameter (1.17.5)
enables
[collection](https://nginx.org/en/docs/http/ngx_http_api_module.html#resolvers_)
of DNS server statistics of requests and responses
in the specified *`zone`*.
The parameter is available as part of our
[commercial subscription](https://nginx.com/products/).

> Since version 1.17.5 and prior to version 1.27.3,
> this directive was available only as part of our
> [commercial subscription](https://nginx.com/products/).

## `resolver_timeout`

**Syntax:** *`time`*

**Default:** 30s

**Contexts:** `upstream`

Sets a timeout for name resolution, for example:
```
resolver_timeout 5s;
```

> Since version 1.17.5 and prior to version 1.27.3,
> this directive was available only as part of our
> [commercial subscription](https://nginx.com/products/).

