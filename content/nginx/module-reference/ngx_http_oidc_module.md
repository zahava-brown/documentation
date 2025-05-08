---
title: "ngx_http_oidc_module"
id: "/en/docs/http/ngx_http_oidc_module.html"
toc: true
---

## `oidc_provider`

**Syntax:** *`name`* `{...}`

**Contexts:** `http`

Defines an OpenID Provider for use with the [`auth_oidc`](https://nginx.org/en/docs/http/ngx_http_oidc_module.html#auth_oidc) directive.

## `auth_oidc`

**Syntax:** *`name`* | `off`

**Default:** off

**Contexts:** `http, server, location`

Enables end user authentication with the
[specified](https://nginx.org/en/docs/http/ngx_http_oidc_module.html#oidc_provider) OpenID Provider.

The special value `off` cancels the effect
of the `auth_oidc` directive
inherited from the previous configuration level.

## `issuer`

**Syntax:** *`URL`*

**Contexts:** `oidc_provider`

Sets the Issuer Identifier URL of the OpenID Provider;
required directive.
The URL must exactly match the value of “`issuer`”
in the OpenID Provider metadata
and requires the “`https`” scheme.

## `client_id`

**Syntax:** *`string`*

**Contexts:** `oidc_provider`

Specifies the client ID of the Relying Party;
required directive.

## `client_secret`

**Syntax:** *`string`*

**Contexts:** `oidc_provider`

Specifies a secret value
used to authenticate the Relying Party with the OpenID Provider.

## `config_url`

**Syntax:** *`URL`*

**Default:** <issuer>/.well-known/openid-configuration

**Contexts:** `oidc_provider`

Sets a custom URL to retrieve the OpenID Provider metadata.

## `cookie_name`

**Syntax:** *`name`*

**Default:** NGX_OIDC_SESSION

**Contexts:** `oidc_provider`

Sets the name of a session cookie.

## `extra_auth_args`

**Syntax:** *`string`*

**Contexts:** `oidc_provider`

Sets additional query arguments for the
[authentication request](https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest) URL.
```
extra_auth_args "display=page&prompt=login";
```

## `redirect_uri`

**Syntax:** *`uri`*

**Default:** /oidc_callback

**Contexts:** `oidc_provider`

Defines the Redirection URI path for post-authentication redirects
expected by the module from the OpenID Provider.
The *`uri`* must match the configuration on the Provider's side.

## `scope`

**Syntax:** *`scope`* ...

**Default:** openid

**Contexts:** `oidc_provider`

Sets requested scopes.
The `openid` scope is always required by OIDC.

## `session_store`

**Syntax:** *`name`*

**Contexts:** `oidc_provider`

Specifies a custom
[key-value database](https://nginx.org/en/docs/http/ngx_http_keyval_module.html#keyval_zone)
that stores session data.
By default, an 8-megabyte key-value database named 
`oidc_default_store_<provider name>`
is created automatically.
> A separate key-value database should be configured for each Provider
> to prevent session reuse across providers.

## `session_timeout`

**Syntax:** *`time`*

**Default:** 8h

**Contexts:** `oidc_provider`

Sets a timeout after which the session is deleted, unless it was
[refreshed](https://openid.net/specs/openid-connect-core-1_0.html#RefreshTokens).

## `ssl_crl`

**Syntax:** *`file`*

**Contexts:** `oidc_provider`

Specifies a *`file`* with revoked certificates (CRL)
in the PEM format used to verify
the certificates of the OpenID Provider endpoints.

## `ssl_trusted_certificate`

**Syntax:** *`file`*

**Default:** system CA bundle

**Contexts:** `oidc_provider`

Specifies a *`file`* with trusted CA certificates in the PEM format
used to verify
the certificates of the OpenID Provider endpoints.

