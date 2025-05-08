---
title: "ngx_stream_mqtt_filter_module"
id: "/en/docs/stream/ngx_stream_mqtt_filter_module.html"
toc: true
---

## `mqtt`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `stream, server`

Enables the MQTT protocol for the given virtual server.

## `mqtt_buffers`

**Syntax:** *`number`* *`size`*

**Default:** 100 1k

**Contexts:** `stream, server`

Sets the *`number`* and *`size`* of the buffers
used for handling MQTT messages,
for a single connection.

## `mqtt_rewrite_buffer_size`

**Syntax:** *`size`*

**Default:** 4k|8k

**Contexts:** `server`

> This directive is obsolete since version 1.25.1.
> The [`mqtt_buffers`](https://nginx.org/en/docs/stream/ngx_stream_mqtt_filter_module.html#mqtt_buffers)
> directive should be used instead.

Sets the *`size`* of the buffer
used for writing a modified message.
By default, the buffer size is equal to one memory page.
This is either 4K or 8K, depending on a platform.
It can be made smaller, however.

## `mqtt_set_connect`

**Syntax:** `field` *`value`*

**Contexts:** `server`

Sets the message `field`
to the given `value` for CONNECT message.
The following fields are supported:
`clientid`,
`username`, and
`password`.
The value can contain text, variables, and their combination.

Several `mqtt_set_connect` directives
can be specified on the same level:
```
mqtt_set_connect clientid "$client";
mqtt_set_connect username "$name";
```

