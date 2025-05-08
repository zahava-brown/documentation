---
title: "ngx_http_v3_module"
id: "/en/docs/http/ngx_http_v3_module.html"
toc: true
---

## `http3`

**Syntax:** `on` | `off`

**Default:** on

**Contexts:** `http, server`

Enables
[HTTP/3](https://datatracker.ietf.org/doc/html/rfc9114)
protocol negotiation.

## `http3_hq`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server`

Enables HTTP/0.9 protocol negotiation
used in
[QUIC interoperability tests](https://github.com/marten-seemann/quic-interop-runner).

## `http3_max_concurrent_streams`

**Syntax:** *`number`*

**Default:** 128

**Contexts:** `http, server`

Sets the maximum number of concurrent HTTP/3 request streams
in a connection.

## `http3_stream_buffer_size`

**Syntax:** *`size`*

**Default:** 64k

**Contexts:** `http, server`

Sets the size of the buffer used for reading and writing of the
QUIC streams.

## `quic_active_connection_id_limit`

**Syntax:** *`number`*

**Default:** 2

**Contexts:** `http, server`

Sets the
QUIC `active_connection_id_limit` transport parameter value.
This is the maximum number of client connection IDs
which can be stored on the server.

## `quic_bpf`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `main`

Enables routing of QUIC packets using
[eBPF](https://ebpf.io/).
When enabled, this allows supporting QUIC connection migration.

> The directive is only supported on Linux 5.7+.

## `quic_gso`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server`

Enables sending in optimized batch mode
using segmentation offloading.

> Optimized sending is supported only on Linux
> featuring `UDP_SEGMENT`.

## `quic_host_key`

**Syntax:** *`file`*

**Contexts:** `http, server`

Sets a *`file`* with the secret key used to encrypt
stateless reset and address validation tokens.
By default, a random key is generated on each reload.
Tokens generated with old keys are not accepted.

## `quic_retry`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server`

Enables the
[QUIC Address Validation](https://datatracker.ietf.org/doc/html/rfc9000#name-address-validation) feature.
This includes sending a new token in a `Retry` packet
or a `NEW_TOKEN` frame
and
validating a token received in the `Initial` packet.

