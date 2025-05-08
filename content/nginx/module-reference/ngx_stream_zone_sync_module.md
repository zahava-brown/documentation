---
title: "ngx_stream_zone_sync_module"
id: "/en/docs/stream/ngx_stream_zone_sync_module.html"
toc: true
---

## `zone_sync`

**Contexts:** `server`

Enables the synchronization of shared memory zones between cluster nodes.
Cluster nodes are defined using [`zone_sync_server`](https://nginx.org/en/docs/stream/ngx_stream_zone_sync_module.html#zone_sync_server) directives.

## `zone_sync_buffers`

**Syntax:** *`number`* *`size`*

**Default:** 8 4k|8k

**Contexts:** `stream, server`

Sets the *`number`* and *`size`* of the
per-zone buffers used for pushing zone contents.
By default, the buffer size is equal to one memory page.
This is either 4K or 8K, depending on a platform.

> A single buffer must be large enough to hold any entry of each zone being
> synchronized.

## `zone_sync_connect_retry_interval`

**Syntax:** *`time`*

**Default:** 1s

**Contexts:** `stream, server`

Defines an interval between connection attempts to another cluster node.

## `zone_sync_connect_timeout`

**Syntax:** *`time`*

**Default:** 5s

**Contexts:** `stream, server`

Defines a timeout for establishing a connection with another cluster node.

## `zone_sync_interval`

**Syntax:** *`time`*

**Default:** 1s

**Contexts:** `stream, server`

Defines an interval for polling updates in a shared memory zone.

## `zone_sync_recv_buffer_size`

**Syntax:** *`size`*

**Default:** 4k|8k

**Contexts:** `stream, server`

Sets *`size`* of a per-connection receive buffer used to parse
incoming stream of synchronization messages.
The buffer size must be equal or greater than one of the
[`zone_sync_buffers`](https://nginx.org/en/docs/stream/ngx_stream_zone_sync_module.html#zone_sync_buffers).
By default, the buffer size is equal to
[zone_sync_buffers](https://nginx.org/en/docs/stream/ngx_stream_zone_sync_module.html#zone_sync_buffers) *`size`*
multiplied by *`number`*.

## `zone_sync_server`

**Syntax:** *`address`* [`resolve`]

**Contexts:** `server`

Defines the *`address`* of a cluster node.
The address can be specified as a domain name or IP address
with a mandatory port, or as a UNIX-domain socket path
specified after the “`unix:`” prefix.
A domain name that resolves to several IP addresses defines
multiple nodes at once.

The `resolve` parameter instructs nginx to monitor
changes of the IP addresses that correspond to a domain name of the node
and automatically modify the configuration
without the need of restarting nginx.

Cluster nodes are specified either dynamically as a single
`zone_sync_server` directive with
the `resolve` parameter, or statically as a series of several
directives without the parameter.
> Each cluster node should be specified only once.

> All cluster nodes should use the same configuration.

In order for the `resolve` parameter to work,
the [`resolver`](https://nginx.org/en/docs/stream/ngx_stream_core_module.html#resolver) directive
must be specified in the
[`stream`](https://nginx.org/en/docs/stream/ngx_stream_core_module.html#stream) block.
Example:
```
stream {
    resolver 10.0.0.1;

    server {
        zone_sync;
        zone_sync_server cluster.example.com:12345 resolve;
        ...
    }
}
```

## `zone_sync_ssl`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `stream, server`

Enables the SSL/TLS protocol for connections to another cluster server.

## `zone_sync_ssl_certificate`

**Syntax:** *`file`*

**Contexts:** `stream, server`

Specifies a *`file`* with the certificate in the PEM format
used for authentication to another cluster server.

## `zone_sync_ssl_certificate_key`

**Syntax:** *`file`*

**Contexts:** `stream, server`

Specifies a *`file`* with the secret key in the PEM format
used for authentication to another cluster server.

## `zone_sync_ssl_ciphers`

**Syntax:** *`ciphers`*

**Default:** DEFAULT

**Contexts:** `stream, server`

Specifies the enabled ciphers for connections to another cluster server.
The ciphers are specified in the format understood by the OpenSSL library.

The full list can be viewed using the
“`openssl ciphers`” command.

## `zone_sync_ssl_conf_command`

**Syntax:** *`name`* *`value`*

**Contexts:** `stream, server`

Sets arbitrary OpenSSL configuration
[commands](https://www.openssl.org/docs/man1.1.1/man3/SSL_CONF_cmd.html)
when establishing a connection with another cluster server.
> The directive is supported when using OpenSSL 1.0.2 or higher.

Several `zone_sync_ssl_conf_command` directives
can be specified on the same level.
These directives are inherited from the previous configuration level
if and only if there are
no `zone_sync_ssl_conf_command` directives
defined on the current level.

> Note that configuring OpenSSL directly
> might result in unexpected behavior.

## `zone_sync_ssl_crl`

**Syntax:** *`file`*

**Contexts:** `stream, server`

Specifies a *`file`* with revoked certificates (CRL)
in the PEM format used to [verify](https://nginx.org/en/docs/stream/ngx_stream_zone_sync_module.html#zone_sync_ssl_verify)
the certificate of another cluster server.

## `zone_sync_ssl_name`

**Syntax:** *`name`*

**Default:** host from zone_sync_server

**Contexts:** `stream, server`

Allows overriding the server name used to
[verify](https://nginx.org/en/docs/stream/ngx_stream_zone_sync_module.html#zone_sync_ssl_verify)
the certificate of a cluster server and to be
[passed through SNI](https://nginx.org/en/docs/stream/ngx_stream_zone_sync_module.html#zone_sync_ssl_server_name)
when establishing a connection with the cluster server.

By default, the host part of the [`zone_sync_server`](https://nginx.org/en/docs/stream/ngx_stream_zone_sync_module.html#zone_sync_server) address is used,
or resolved IP address if the [`resolve`](https://nginx.org/en/docs/stream/ngx_stream_zone_sync_module.html#resolve) parameter is specified.

## `zone_sync_ssl_password_file`

**Syntax:** *`file`*

**Contexts:** `stream, server`

Specifies a *`file`* with passphrases for
[secret keys](https://nginx.org/en/docs/stream/ngx_stream_zone_sync_module.html#zone_sync_ssl_certificate_key)
where each passphrase is specified on a separate line.
Passphrases are tried in turn when loading the key.

## `zone_sync_ssl_protocols`

**Syntax:** [`SSLv2`] [`SSLv3`] [`TLSv1`] [`TLSv1.1`] [`TLSv1.2`] [`TLSv1.3`]

**Default:** TLSv1.2 TLSv1.3

**Contexts:** `stream, server`

Enables the specified protocols for connections to another cluster server.

## `zone_sync_ssl_server_name`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `stream, server`

Enables or disables passing of the server name through
[TLS Server Name Indication extension](http://en.wikipedia.org/wiki/Server_Name_Indication) (SNI, RFC 6066)
when establishing a connection with another cluster server.

## `zone_sync_ssl_trusted_certificate`

**Syntax:** *`file`*

**Contexts:** `stream, server`

Specifies a *`file`* with trusted CA certificates in the PEM format
used to [verify](https://nginx.org/en/docs/stream/ngx_stream_zone_sync_module.html#zone_sync_ssl_verify)
the certificate of another cluster server.

## `zone_sync_ssl_verify`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `stream, server`

Enables or disables verification of another cluster server certificate.

## `zone_sync_ssl_verify_depth`

**Syntax:** *`number`*

**Default:** 1

**Contexts:** `stream, server`

Sets the verification depth in another cluster server certificates chain.

## `zone_sync_timeout`

**Syntax:** *`timeout`*

**Default:** 5s

**Contexts:** `stream, server`

Sets the *`timeout`* between two successive
read or write operations on connection to another cluster node.
If no data is transmitted within this time, the connection is closed.

