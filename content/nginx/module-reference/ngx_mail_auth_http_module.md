---
title: "ngx_mail_auth_http_module"
id: "/en/docs/mail/ngx_mail_auth_http_module.html"
toc: true
---

## `auth_http`

**Syntax:** *`URL`*

**Contexts:** `mail, server`

Sets the URL of the HTTP authentication server.
The protocol is described [below](https://nginx.org/en/docs/mail/ngx_mail_auth_http_module.html#protocol).

## `auth_http_header`

**Syntax:** *`header`* *`value`*

**Contexts:** `mail, server`

Appends the specified header to requests sent to the authentication server.
This header can be used as the shared secret to verify
that the request comes from nginx.
For example:
```
auth_http_header X-Auth-Key "secret_string";
```

## `auth_http_pass_client_cert`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `mail, server`

Appends the "Auth-SSL-Cert" header with the
[client](https://nginx.org/en/docs/mail/ngx_mail_ssl_module.html#ssl_verify_client)
certificate in the PEM format (urlencoded)
to requests sent to the authentication server.

## `auth_http_timeout`

**Syntax:** *`time`*

**Default:** 60s

**Contexts:** `mail, server`

Sets the timeout for communication with the authentication server.

