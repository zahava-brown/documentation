---
title: "ngx_otel_module"
id: "/en/docs/ngx_otel_module.html"
toc: true
---

## `otel_exporter`

**Syntax:**  `{...}`

**Contexts:** `http`

Specifies OTel data export parameters:

- `
    endpoint [(http|https)://]host:port;`

    OTLP/gRPC endpoint that will accept telemetry data.
    TLS is supported since 0.1.2.
- `
    trusted_certificate path;`

    the CA certificates file in PEM format used to verify TLS endpoint
    (since 0.1.2).
    Defaults to OS provided CA bundle.
- `header name value;`

    a custom HTTP header to add to telemetry export request (since 0.1.2).
- `interval time;`

    the maximum interval between two exports,
    by default is `5` seconds.
- `batch_size number;`

    the maximum number of spans to be sent in one batch per worker,
    by default is `512`.
- `batch_count number;`

    the number of pending batches per worker,
    spans exceeding the limit are dropped,
    by default is `4`.

Example:
```
otel_exporter {
    endpoint https://otel-example.nginx.com:4317;

    header X-API-Token "my-token-value";
}
```

## `otel_service_name`

**Syntax:** *`name`*

**Default:** unknown_service:nginx

**Contexts:** `http`

Sets the
“[`service.name`](https://opentelemetry.io/docs/reference/specification/resource/semantic_conventions/#service)”
attribute of the OTel resource.

## `otel_resource_attr`

**Syntax:** *`name`* *`value`*

**Contexts:** `http`

Sets a custom OTel resource attribute.

## `otel_trace`

**Syntax:** `on` | `off` | `$variable`

**Default:** off

**Contexts:** `http, server, location`

Enables or disables OpenTelemetry tracing.
The directive can also be enabled by specifying a variable:
```
split_clients "$otel_trace_id" $ratio_sampler {
              10%              on;
              *                off;
}

server {
    location / {
        otel_trace         $ratio_sampler;
        otel_trace_context inject;
        proxy_pass         http://backend;
    }
}
```

## `otel_trace_context`

**Syntax:** `extract` | `inject` | `propagate` | `ignore`

**Default:** ignore

**Contexts:** `http, server, location`

Specifies how to propagate
[traceparent/tracestate](https://www.w3.org/TR/trace-context/#design-overview) headers:

- `extract`

    uses an existing trace context from the request,
    so that the identifiers of
    a [trace](https://nginx.org/en/docs/ngx_otel_module.html#var_otel_trace_id) and
    the [parent span](https://nginx.org/en/docs/ngx_otel_module.html#var_otel_parent_id)
    are inherited from the incoming request.
- `inject`

    adds a new context to the request, overwriting existing headers, if any.
- `propagate`

    updates the existing context
    (combines [`extract`](https://nginx.org/en/docs/ngx_otel_module.html#extract) and [`inject`](https://nginx.org/en/docs/ngx_otel_module.html#inject)).
- `ignore`

    skips context headers processing.

## `otel_span_name`

**Syntax:** *`name`*

**Contexts:** `http, server, location`

Defines the name of the OTel
[span](https://opentelemetry.io/docs/concepts/observability-primer/#spans).
By default, it is a name of the location for a request.
The name can contain variables.

## `otel_span_attr`

**Syntax:** *`name`* *`value`*

**Contexts:** `http, server, location`

Adds a custom OTel span attribute.
The value can contain variables.

