---
title: "ngx_stream_ssl_module"
id: "/en/docs/stream/ngx_stream_ssl_module.html"
toc: true
---

## `ssl_alpn`

**Syntax:** *`protocol`* ...

**Contexts:** `stream, server`

Specifies the list of supported
[ALPN](https://datatracker.ietf.org/doc/html/rfc7301) protocols.
One of the protocols must be
[negotiated](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#var_ssl_alpn_protocol) if the client uses ALPN:
```
map $ssl_alpn_protocol $proxy {
    h2                127.0.0.1:8001;
    http/1.1          127.0.0.1:8002;
}

server {
    listen      12346;
    proxy_pass  $proxy;
    ssl_alpn    h2 http/1.1;
}
```

## `ssl_certificate`

**Syntax:** *`file`*

**Contexts:** `stream, server`

Specifies a *`file`* with the certificate in the PEM format
for the given virtual server.
If intermediate certificates should be specified in addition to a primary
certificate, they should be specified in the same file in the following
order: the primary certificate comes first, then the intermediate certificates.
A secret key in the PEM format may be placed in the same file.

Since version 1.11.0,
this directive can be specified multiple times
to load certificates of different types, for example, RSA and ECDSA:
```
server {
    listen              12345 ssl;

    ssl_certificate     example.com.rsa.crt;
    ssl_certificate_key example.com.rsa.key;

    ssl_certificate     example.com.ecdsa.crt;
    ssl_certificate_key example.com.ecdsa.key;

    ...
}
```
> Only OpenSSL 1.0.2 or higher supports separate
> [certificate chains](https://nginx.org/en/docs/http/configuring_https_servers.html#chains)
> for different certificates.
> With older versions, only one certificate chain can be used.

Since version 1.15.9, variables can be used in the *`file`* name
when using OpenSSL 1.0.2 or higher:
```
ssl_certificate     $ssl_server_name.crt;
ssl_certificate_key $ssl_server_name.key;
```
Note that using variables implies that
a certificate will be loaded for each SSL handshake,
and this may have a negative impact on performance.

The value
`data`:*`$variable`*
can be specified instead of the *`file`* (1.15.10),
which loads a certificate from a variable
without using intermediate files.
Note that inappropriate use of this syntax may have its security implications,
such as writing secret key data to
[error log](https://nginx.org/en/docs/ngx_core_module.html#error_log).

It should be kept in mind that due to the SSL/TLS protocol limitations,
for maximum interoperability with clients that do not use
[SNI](http://en.wikipedia.org/wiki/Server_Name_Indication),
virtual servers with different certificates should listen on
[different IP addresses](https://nginx.org/en/docs/http/configuring_https_servers.html#name_based_https_servers).

## `ssl_certificate_cache`

**Syntax:** `off`

**Default:** off

**Contexts:** `stream, server`

Defines a cache that stores
[SSL certificates](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_certificate) and
[secret keys](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_certificate_key)
specified with [variables](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_certificate_key_variables).

The directive has the following parameters:
- `max`

    sets the maximum number of elements in the cache;
    on cache overflow the least recently used (LRU) elements are removed;
- `inactive`

    defines a time after which an element is removed from the cache
    if it has not been accessed during this time;
    by default, it is 10 seconds;
- `valid`

    defines a time during which
    an element in the cache is considered valid
    and can be reused;
    by default, it is 60 seconds.
    Certificates that exceed this time will be reloaded or revalidated;
- `off`

    disables the cache.

Example:
```
ssl_certificate       $ssl_server_name.crt;
ssl_certificate_key   $ssl_server_name.key;
ssl_certificate_cache max=1000 inactive=20s valid=1m;
```

## `ssl_certificate_key`

**Syntax:** *`file`*

**Contexts:** `stream, server`

Specifies a *`file`* with the secret key in the PEM format
for the given virtual server.

The value
`engine`:*`name`*:*`id`*
can be specified instead of the *`file`*,
which loads a secret key with a specified *`id`*
from the OpenSSL engine *`name`*.

The value
`data`:*`$variable`*
can be specified instead of the *`file`* (1.15.10),
which loads a secret key from a variable without using intermediate files.
Note that inappropriate use of this syntax may have its security implications,
such as writing secret key data to
[error log](https://nginx.org/en/docs/ngx_core_module.html#error_log).

Since version 1.15.9, variables can be used in the *`file`* name
when using OpenSSL 1.0.2 or higher.

## `ssl_ciphers`

**Syntax:** *`ciphers`*

**Default:** HIGH:!aNULL:!MD5

**Contexts:** `stream, server`

Specifies the enabled ciphers.
The ciphers are specified in the format understood by the
OpenSSL library, for example:
```
ssl_ciphers ALL:!aNULL:!EXPORT56:RC4+RSA:+HIGH:+MEDIUM:+LOW:+SSLv2:+EXP;
```

The full list can be viewed using the
“`openssl ciphers`” command.

## `ssl_client_certificate`

**Syntax:** *`file`*

**Contexts:** `stream, server`

Specifies a *`file`* with trusted CA certificates in the PEM format
used to [verify](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_verify_client) client certificates and
OCSP responses if [`ssl_stapling`](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_stapling) is enabled.

The list of certificates will be sent to clients.
If this is not desired, the [`ssl_trusted_certificate`](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_trusted_certificate)
directive can be used.

## `ssl_conf_command`

**Syntax:** *`name`* *`value`*

**Contexts:** `stream, server`

Sets arbitrary OpenSSL configuration
[commands](https://www.openssl.org/docs/man1.1.1/man3/SSL_CONF_cmd.html).
> The directive is supported when using OpenSSL 1.0.2 or higher.

Several `ssl_conf_command` directives
can be specified on the same level:
```
ssl_conf_command Options PrioritizeChaCha;
ssl_conf_command Ciphersuites TLS_CHACHA20_POLY1305_SHA256;
```
These directives are inherited from the previous configuration level
if and only if there are no `ssl_conf_command` directives
defined on the current level.

> Note that configuring OpenSSL directly
> might result in unexpected behavior.

## `ssl_crl`

**Syntax:** *`file`*

**Contexts:** `stream, server`

Specifies a *`file`* with revoked certificates (CRL)
in the PEM format used to [verify](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_verify_client)
client certificates.

## `ssl_dhparam`

**Syntax:** *`file`*

**Contexts:** `stream, server`

Specifies a *`file`* with DH parameters for DHE ciphers.

By default no parameters are set,
and therefore DHE ciphers will not be used.
> Prior to version 1.11.0, builtin parameters were used by default.

## `ssl_ecdh_curve`

**Syntax:** *`curve`*

**Default:** auto

**Contexts:** `stream, server`

Specifies a *`curve`* for ECDHE ciphers.

When using OpenSSL 1.0.2 or higher,
it is possible to specify multiple curves (1.11.0), for example:
```
ssl_ecdh_curve prime256v1:secp384r1;
```

The special value `auto` (1.11.0) instructs nginx to use
a list built into the OpenSSL library when using OpenSSL 1.0.2 or higher,
or `prime256v1` with older versions.

> Prior to version 1.11.0,
> the `prime256v1` curve was used by default.

> When using OpenSSL 1.0.2 or higher,
> this directive sets the list of curves supported by the server.
> Thus, in order for ECDSA certificates to work,
> it is important to include the curves used in the certificates.

## `ssl_handshake_timeout`

**Syntax:** *`time`*

**Default:** 60s

**Contexts:** `stream, server`

Specifies a timeout for the SSL handshake to complete.

## `ssl_key_log`

**Syntax:** path

**Contexts:** `stream, server`

Enables logging of client connection SSL keys
and specifies the path to the key log file.
Keys are logged in the
[SSLKEYLOGFILE](https://datatracker.ietf.org/doc/html/draft-ietf-tls-keylogfile)
format compatible with Wireshark.

> This directive is available as part of our
> [commercial subscription](https://nginx.com/products/).

## `ssl_ocsp`

**Syntax:** `on` | `off` | `leaf`

**Default:** off

**Contexts:** `stream, server`

Enables OCSP validation of the client certificate chain.
The `leaf` parameter
enables validation of the client certificate only.

For the OCSP validation to work,
the [`ssl_verify_client`](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_verify_client) directive should be set to
`on` or `optional`.

To resolve the OCSP responder hostname,
the [`resolver`](https://nginx.org/en/docs/stream/ngx_stream_core_module.html#resolver) directive
should also be specified.

Example:
```
ssl_verify_client on;
ssl_ocsp          on;
resolver          192.0.2.1;
```

## `ssl_ocsp_cache`

**Syntax:** `off` | [`shared`:*`name`*:*`size`*]

**Default:** off

**Contexts:** `stream, server`

Sets `name` and `size` of the cache
that stores client certificates status for OCSP validation.
The cache is shared between all worker processes.
A cache with the same name can be used in several
virtual servers.

The `off` parameter prohibits the use of the cache.

## `ssl_ocsp_responder`

**Syntax:** *`url`*

**Contexts:** `stream, server`

Overrides the URL of the OCSP responder specified in the
“[Authority Information Access](https://datatracker.ietf.org/doc/html/rfc5280#section-4.2.2.1)” certificate extension
for [validation](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_ocsp) of client certificates.

Only “`http://`” OCSP responders are supported:
```
ssl_ocsp_responder http://ocsp.example.com/;
```

## `ssl_password_file`

**Syntax:** *`file`*

**Contexts:** `stream, server`

Specifies a *`file`* with passphrases for
[secret keys](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_certificate_key)
where each passphrase is specified on a separate line.
Passphrases are tried in turn when loading the key.

Example:
```
stream {
    ssl_password_file /etc/keys/global.pass;
    ...

    server {
        listen 127.0.0.1:12345;
        ssl_certificate_key /etc/keys/first.key;
    }

    server {
        listen 127.0.0.1:12346;

        # named pipe can also be used instead of a file
        ssl_password_file /etc/keys/fifo;
        ssl_certificate_key /etc/keys/second.key;
    }
}
```

## `ssl_prefer_server_ciphers`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `stream, server`

Specifies that server ciphers should be preferred over client ciphers
when the SSLv3 and TLS protocols are used.

## `ssl_protocols`

**Syntax:** [`SSLv2`] [`SSLv3`] [`TLSv1`] [`TLSv1.1`] [`TLSv1.2`] [`TLSv1.3`]

**Default:** TLSv1.2 TLSv1.3

**Contexts:** `stream, server`

Enables the specified protocols.

If the directive is specified
on the [`server`](https://nginx.org/en/docs/stream/ngx_stream_core_module.html#server) level,
the value from the default server can be used.
Details are provided in the
“[Virtual server selection](https://nginx.org/en/docs/http/server_names.html#virtual_server_selection)” section.

> The `TLSv1.1` and `TLSv1.2` parameters
> work only when OpenSSL 1.0.1 or higher is used.

> The `TLSv1.3` parameter (1.13.0) works only when
> OpenSSL 1.1.1 or higher is used.

> The `TLSv1.3` parameter is used by default
> since 1.23.4.

## `ssl_reject_handshake`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `stream, server`

If enabled, SSL handshakes in
the [`server`](https://nginx.org/en/docs/stream/ngx_stream_core_module.html#server) block will be rejected.

For example, in the following configuration, SSL handshakes with
server names other than `example.com` are rejected:
```
server {
    listen               443 ssl default_server;
    ssl_reject_handshake on;
}

server {
    listen              443 ssl;
    server_name         example.com;
    ssl_certificate     example.com.crt;
    ssl_certificate_key example.com.key;
}
```

## `ssl_session_cache`

**Syntax:** `off` | `none` | [`builtin`[:*`size`*]] [`shared`:*`name`*:*`size`*]

**Default:** none

**Contexts:** `stream, server`

Sets the types and sizes of caches that store session parameters.
A cache can be of any of the following types:
- `off`

    the use of a session cache is strictly prohibited:
    nginx explicitly tells a client that sessions may not be reused.
- `none`

    the use of a session cache is gently disallowed:
    nginx tells a client that sessions may be reused, but does not
    actually store session parameters in the cache.
- `builtin`

    a cache built in OpenSSL; used by one worker process only.
    The cache size is specified in sessions.
    If size is not given, it is equal to 20480 sessions.
    Use of the built-in cache can cause memory fragmentation.
- `shared`

    a cache shared between all worker processes.
    The cache size is specified in bytes; one megabyte can store
    about 4000 sessions.
    Each shared cache should have an arbitrary name.
    A cache with the same name can be used in several
    virtual servers.
    It is also used to automatically generate, store, and
    periodically rotate TLS session ticket keys (1.23.2)
    unless configured explicitly
    using the [`ssl_session_ticket_key`](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_session_ticket_key) directive.

Both cache types can be used simultaneously, for example:
```
ssl_session_cache builtin:1000 shared:SSL:10m;
```
but using only shared cache without the built-in cache should
be more efficient.

## `ssl_session_ticket_key`

**Syntax:** *`file`*

**Contexts:** `stream, server`

Sets a *`file`* with the secret key used to encrypt
and decrypt TLS session tickets.
The directive is necessary if the same key has to be shared between
multiple servers.
By default, a randomly generated key is used.

If several keys are specified, only the first key is
used to encrypt TLS session tickets.
This allows configuring key rotation, for example:
```
ssl_session_ticket_key current.key;
ssl_session_ticket_key previous.key;
```

The *`file`* must contain 80 or 48 bytes
of random data and can be created using the following command:
```
openssl rand 80 > ticket.key
```
Depending on the file size either AES256 (for 80-byte keys, 1.11.8)
or AES128 (for 48-byte keys) is used for encryption.

## `ssl_session_tickets`

**Syntax:** `on` | `off`

**Default:** on

**Contexts:** `stream, server`

Enables or disables session resumption through
[TLS session tickets](https://datatracker.ietf.org/doc/html/rfc5077).

## `ssl_session_timeout`

**Syntax:** *`time`*

**Default:** 5m

**Contexts:** `stream, server`

Specifies a time during which a client may reuse the
session parameters.

## `ssl_stapling`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `stream, server`

Enables or disables
[stapling of OCSP responses](https://datatracker.ietf.org/doc/html/rfc6066#section-8) by the server.
Example:
```
ssl_stapling on;
resolver 192.0.2.1;
```

For the OCSP stapling to work, the certificate of the server certificate
issuer should be known.
If the [`ssl_certificate`](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_certificate) file does
not contain intermediate certificates,
the certificate of the server certificate issuer should be
present in the
[`ssl_trusted_certificate`](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_trusted_certificate) file.

For a resolution of the OCSP responder hostname,
the [`resolver`](https://nginx.org/en/docs/stream/ngx_stream_core_module.html#resolver) directive
should also be specified.

## `ssl_stapling_file`

**Syntax:** *`file`*

**Contexts:** `stream, server`

When set, the stapled OCSP response will be taken from the
specified *`file`* instead of querying
the OCSP responder specified in the server certificate.

The file should be in the DER format as produced by the
“`openssl ocsp`” command.

## `ssl_stapling_responder`

**Syntax:** *`url`*

**Contexts:** `stream, server`

Overrides the URL of the OCSP responder specified in the
“[Authority Information Access](https://datatracker.ietf.org/doc/html/rfc5280#section-4.2.2.1)” certificate extension.

Only “`http://`” OCSP responders are supported:
```
ssl_stapling_responder http://ocsp.example.com/;
```

## `ssl_stapling_verify`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `stream, server`

Enables or disables verification of OCSP responses by the server.

For verification to work, the certificate of the server certificate
issuer, the root certificate, and all intermediate certificates
should be configured as trusted using the
[`ssl_trusted_certificate`](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_trusted_certificate) directive.

## `ssl_trusted_certificate`

**Syntax:** *`file`*

**Contexts:** `stream, server`

Specifies a *`file`* with trusted CA certificates in the PEM format
used to [verify](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_verify_client) client certificates and
OCSP responses if [`ssl_stapling`](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_stapling) is enabled.

In contrast to the certificate set by [`ssl_client_certificate`](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#ssl_client_certificate),
the list of these certificates will not be sent to clients.

## `ssl_verify_client`

**Syntax:** `on` | `off` | `optional` | `optional_no_ca`

**Default:** off

**Contexts:** `stream, server`

Enables verification of client certificates.
The verification result is stored in the
[$ssl_client_verify](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#var_ssl_client_verify) variable.
If an error has occurred during the client certificate verification
or a client has not presented the required certificate,
the connection is closed.

The `optional` parameter requests the client
certificate and verifies it if the certificate is present.

The `optional_no_ca` parameter
requests the client
certificate but does not require it to be signed by a trusted CA certificate.
This is intended for the use in cases when a service that is external to nginx
performs the actual certificate verification.
The contents of the certificate is accessible through the
[$ssl_client_cert](https://nginx.org/en/docs/stream/ngx_stream_ssl_module.html#var_ssl_client_cert) variable.

## `ssl_verify_depth`

**Syntax:** *`number`*

**Default:** 1

**Contexts:** `stream, server`

Sets the verification depth in the client certificates chain.

