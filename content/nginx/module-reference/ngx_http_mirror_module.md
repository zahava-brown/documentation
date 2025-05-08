---
title: "ngx_http_mirror_module"
id: "/en/docs/http/ngx_http_mirror_module.html"
toc: true
---

## `mirror`

**Syntax:** *`uri`* | `off`

**Default:** off

**Contexts:** `http, server, location`

Sets the URI to which an original request will be mirrored.
Several mirrors can be specified on the same configuration level.

## `mirror_request_body`

**Syntax:** `on` | `off`

**Default:** on

**Contexts:** `http, server, location`

Indicates whether the client request body is mirrored.
When enabled, the client request body will be read
prior to creating mirror subrequests.
In this case, unbuffered client request body proxying
set by the
[`proxy_request_buffering`](https://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_request_buffering),
[`fastcgi_request_buffering`](https://nginx.org/en/docs/http/ngx_http_fastcgi_module.html#fastcgi_request_buffering),
[`scgi_request_buffering`](https://nginx.org/en/docs/http/ngx_http_scgi_module.html#scgi_request_buffering),
and
[`uwsgi_request_buffering`](https://nginx.org/en/docs/http/ngx_http_uwsgi_module.html#uwsgi_request_buffering)
directives will be disabled.
```
location / {
    mirror /mirror;
    mirror_request_body off;
    proxy_pass http://backend;
}

location = /mirror {
    internal;
    proxy_pass http://log_backend;
    proxy_pass_request_body off;
    proxy_set_header Content-Length "";
    proxy_set_header X-Original-URI $request_uri;
}
```

