---
title: "ngx_mgmt_module"
id: "/en/docs/ngx_mgmt_module.html"
toc: true
---

## `mgmt`

**Syntax:**  `{...}`

**Contexts:** `main`

Provides the configuration file context in which
usage reporting and license management directives
are specified.

## `enforce_initial_report`

**Syntax:** `on` | `off`

**Default:** on

**Contexts:** `mgmt`

Enables or disables the 180-day grace period
for sending the initial usage report.

The initial usage report is sent immediately
upon nginx first start after installation.
By default, if the initial report is not received by F5 licensing endpoint,
nginx stops processing traffic until the report is successfully delivered.
Setting the directive value to `off` enables
the 180-day grace period during which
the initial usage report must be received by F5 licensing endpoint.

## `license_token`

**Syntax:** *`file`*

**Default:** license.jwt

**Contexts:** `mgmt`

Specifies a JWT license *`file`*.
By default, the *`license.jwt`* file is expected to be at
`/etc/nginx/` for Linux or at
`/usr/local/etc/nginx/` for FreeBSD.

## `proxy`

**Syntax:** *`host`*:*`port`*

**Contexts:** `mgmt`

Sets the HTTP CONNECT proxy
used for sending the usage report.

## `proxy_username`

**Syntax:** *`string`*

**Contexts:** `mgmt`

Sets the user name used for authentication on
the [proxy](https://nginx.org/en/docs/ngx_mgmt_module.html#proxy).

## `proxy_password`

**Syntax:** *`string`*

**Contexts:** `mgmt`

Sets the password used for authentication on
the [proxy](https://nginx.org/en/docs/ngx_mgmt_module.html#proxy).

The password is sent unencrypted by default.
If the proxy supports TLS, the connection to the proxy can be
protected with the [stream](https://nginx.org/en/docs/stream/ngx_stream_proxy_module.html)
module:
```
mgmt {
    proxy          127.0.0.1:8080;
    proxy_username <name>;
    proxy_password <password>;
}

stream {
    server {
        listen 127.0.0.1:8080;
        
        proxy_ssl                     on;
        proxy_ssl_verify              on;
        proxy_ssl_trusted_certificate <proxy_ca_file>;

        proxy_pass <proxy_host>:<proxy_port>;
    }
}
```

## `resolver`

**Syntax:** *`address`* ... [`valid`=*`time`*] [`ipv4`=`on`|`off`] [`ipv6`=`on`|`off`] [`status_zone`=*`zone`*]

**Contexts:** `mgmt`

Configures name servers used to resolve usage reporting endpoint name.
By default, the system resolver is used.

See [`resolver`](https://nginx.org/en/docs/http/ngx_http_core_module.html#resolver) for details.

## `ssl_crl`

**Syntax:** *`file`*

**Contexts:** `mgmt`

Specifies a *`file`* with revoked certificates (CRL)
in the PEM format used to [verify](https://nginx.org/en/docs/ngx_mgmt_module.html#ssl_verify)
the certificate of the usage reporting endpoint.

## `ssl_trusted_certificate`

**Syntax:** *`file`*

**Default:** system CA bundle

**Contexts:** `mgmt`

Specifies a *`file`* with trusted CA certificates in the PEM format
used to [verify](https://nginx.org/en/docs/ngx_mgmt_module.html#ssl_verify)
the certificate of the usage reporting endpoint.

## `ssl_verify`

**Syntax:** `on` | `off`

**Default:** on

**Contexts:** `mgmt`

Enables or disables verification of the usage reporting endpoint certificate.

> Before 1.27.2, the default value was `off`.

## `state_path`

**Syntax:** *`path`*

**Contexts:** `mgmt`

Defines a directory for storing state files
(`nginx-mgmt-*`)
created by the `ngx_mgmt_module` module.
The default directory
for Linux is `/var/lib/nginx/state`,
for FreeBSD is `/var/db/nginx/state`.

## `usage_report`

**Syntax:** [`endpoint`=*`address`*] [`interval`=*`time`*]

**Default:** endpoint=product.connect.nginx.com interval=1h

**Contexts:** `mgmt`

Sets the *`address`* and *`port`*
of the usage reporting endpoint.
The `interval` parameter sets an interval between
two consecutive reports.
> Before 1.27.2, the default values were
> `nginx-mgmt.local` and
> `30m`.

