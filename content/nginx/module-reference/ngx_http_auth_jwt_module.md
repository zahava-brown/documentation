---
title: "ngx_http_auth_jwt_module"
id: "/en/docs/http/ngx_http_auth_jwt_module.html"
toc: true
---

## `auth_jwt`

**Syntax:** *`string`* [`token=`*`$variable`*] | `off`

**Default:** off

**Contexts:** `http, server, location, limit_except`

Enables validation of JSON Web Token.
The specified *`string`* is used as a `realm`.
Parameter value can contain variables.

The optional `token` parameter specifies a variable
that contains JSON Web Token.
By default, JWT is passed in the "Authorization" header
as a
[Bearer Token](https://datatracker.ietf.org/doc/html/rfc6750).
JWT may be also passed as a cookie or a part of a query string:
```
auth_jwt "closed site" token=$cookie_auth_token;
```

The special value `off` cancels the effect
of the `auth_jwt` directive
inherited from the previous configuration level.

## `auth_jwt_claim_set`

**Syntax:** *`$variable`* *`name`* ...

**Contexts:** `http`

Sets the *`variable`* to a JWT claim parameter
identified by key names.
Name matching starts from the top level of the JSON tree.
For arrays, the variable keeps a list of array elements separated by commas.
```
auth_jwt_claim_set $email info e-mail;
auth_jwt_claim_set $job info "job title";
```
> Prior to version 1.13.7, only one key name could be specified,
> and the result was undefined for arrays.

> Variable values for tokens encrypted with JWE
> are available only after decryption which occurs during the
> [Access](https://nginx.org/en/docs/dev/development_guide.html#http_phases) phase.

## `auth_jwt_header_set`

**Syntax:** *`$variable`* *`name`* ...

**Contexts:** `http`

Sets the *`variable`* to a JOSE header parameter
identified by key names.
Name matching starts from the top level of the JSON tree.
For arrays, the variable keeps a list of array elements separated by commas.
> Prior to version 1.13.7, only one key name could be specified,
> and the result was undefined for arrays.

## `auth_jwt_key_cache`

**Syntax:** *`time`*

**Default:** 0

**Contexts:** `http, server, location`

Enables or disables caching of keys
obtained from a [file](https://nginx.org/en/docs/http/ngx_http_auth_jwt_module.html#auth_jwt_key_file)
or from a [subrequest](https://nginx.org/en/docs/http/ngx_http_auth_jwt_module.html#auth_jwt_key_request),
and sets caching time for them.
Caching of keys obtained from variables is not supported.
By default, caching of keys is disabled.

## `auth_jwt_key_file`

**Syntax:** *`file`*

**Contexts:** `http, server, location, limit_except`

Specifies a *`file`* in
[JSON Web Key Set](https://datatracker.ietf.org/doc/html/rfc7517#section-5)
format for validating JWT signature.
Parameter value can contain variables.

Several `auth_jwt_key_file` directives
can be specified on the same level (1.21.1):
```
auth_jwt_key_file conf/keys.json;
auth_jwt_key_file conf/key.jwk;
```
If at least one of the specified keys cannot be loaded or processed,
nginx will return the
500 (Internal Server Error) error.

## `auth_jwt_key_request`

**Syntax:** *`uri`*

**Contexts:** `http, server, location, limit_except`

Allows retrieving a
[JSON Web Key Set](https://datatracker.ietf.org/doc/html/rfc7517#section-5)
file from a subrequest for validating JWT signature and
sets the URI where the subrequest will be sent to.
Parameter value can contain variables.
To avoid validation overhead,
it is recommended to cache the key file:
```
proxy_cache_path /data/nginx/cache levels=1 keys_zone=foo:10m;

server {
    ...

    location / {
        auth_jwt             "closed site";
        auth_jwt_key_request /jwks_uri;
    }

    location = /jwks_uri {
        internal;
        proxy_cache foo;
        proxy_pass  http://idp.example.com/keys;
    }
}
```
Several `auth_jwt_key_request` directives
can be specified on the same level (1.21.1):
```
auth_jwt_key_request /jwks_uri;
auth_jwt_key_request /jwks2_uri;
```
If at least one of the specified keys cannot be loaded or processed,
nginx will return the
500 (Internal Server Error) error.

## `auth_jwt_leeway`

**Syntax:** *`time`*

**Default:** 0s

**Contexts:** `http, server, location`

Sets the maximum allowable leeway to compensate
clock skew when verifying the
[exp](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1.4)
and
[nbf](https://datatracker.ietf.org/doc/html/rfc7519#section-4.1.5)
JWT claims.

## `auth_jwt_type`

**Syntax:** `signed` | `encrypted` | `nested`

**Default:** signed

**Contexts:** `http, server, location, limit_except`

Specifies which type of JSON Web Token to expect:
JWS (`signed`),
JWE (`encrypted`),
or signed and then encrypted
Nested JWT (`nested`) (1.21.0).

## `auth_jwt_require`

**Syntax:** *`$value`* ... [`error`=`401` | `403`]

**Contexts:** `http, server, location, limit_except`

Specifies additional checks for JWT validation.
The value can contain text, variables, and their combination,
and must start with a variable (1.21.7).
The authentication will succeed only
if all the values are not empty and are not equal to “0”.
```
map $jwt_claim_iss $valid_jwt_iss {
    "good" 1;
}
...

auth_jwt_require $valid_jwt_iss;
```

If any of the checks fails,
the `401` error code is returned.
The optional `error` parameter (1.21.7)
allows redefining the error code to `403`.

