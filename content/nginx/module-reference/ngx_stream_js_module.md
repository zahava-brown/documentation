---
title: "ngx_stream_js_module"
id: "/en/docs/stream/ngx_stream_js_module.html"
toc: true
---

## `js_access`

**Syntax:** *`function`* | *`module.function`*

**Contexts:** `stream, server`

Sets an njs function which will be called at the
[access](https://nginx.org/en/docs/stream/stream_processing.html#access_phase) phase.
Since [0.4.0](https://nginx.org/en/docs/njs/changes.html#njs0.4.0),
a module function can be referenced.

The function is called once at the moment when the stream session reaches
the [access](https://nginx.org/en/docs/stream/stream_processing.html#access_phase) phase
for the first time.
The function is called with the following arguments:

- `s`

    the [Stream Session](https://nginx.org/en/docs/njs/reference.html#stream) object

At this phase, it is possible to perform initialization
or register a callback with
the [`s.on()`](https://nginx.org/en/docs/njs/reference.html#s_on)
method
for each incoming data chunk until one of the following methods are called:
[`s.allow()`](https://nginx.org/en/docs/njs/reference.html#s_allow),
[`s.decline()`](https://nginx.org/en/docs/njs/reference.html#s_decline),
[`s.done()`](https://nginx.org/en/docs/njs/reference.html#s_done).
As soon as one of these methods is called, the stream session processing
switches to the [next phase](https://nginx.org/en/docs/stream/stream_processing.html)
and all current
[`s.on()`](https://nginx.org/en/docs/njs/reference.html#s_on)
callbacks are dropped.

## `js_context_reuse`

**Syntax:** *`number`*

**Default:** 128

**Contexts:** `stream, server`

Sets a maximum number of JS context to be reused for
[QuickJS engine](https://nginx.org/en/docs/njs/engine.html).
Each context is used for a single stream session.
The finished context is put into a pool of reusable contexts.
If the pool is full, the context is destroyed.

## `js_engine`

**Syntax:** `njs` | `qjs`

**Default:** njs

**Contexts:** `stream, server`

Sets a [JavaScript engine](https://nginx.org/en/docs/njs/engine.html)
to be used for njs scripts.
The `njs` parameter sets the njs engine, also used by default.
The `qjs` parameter sets the QuickJS engine.

## `js_fetch_buffer_size`

**Syntax:** *`size`*

**Default:** 16k

**Contexts:** `stream, server`

Sets the *`size`* of the buffer used for reading and writing
with [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).

## `js_fetch_ciphers`

**Syntax:** *`ciphers`*

**Default:** HIGH:!aNULL:!MD5

**Contexts:** `stream, server`

Specifies the enabled ciphers for HTTPS connections
with [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).
The ciphers are specified in the format understood by the OpenSSL library.

The full list can be viewed using the
“`openssl ciphers`” command.

## `js_fetch_max_response_buffer_size`

**Syntax:** *`size`*

**Default:** 1m

**Contexts:** `stream, server`

Sets the maximum *`size`* of the response received
with [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).

## `js_fetch_protocols`

**Syntax:** [`TLSv1`] [`TLSv1.1`] [`TLSv1.2`] [`TLSv1.3`]

**Default:** TLSv1 TLSv1.1 TLSv1.2

**Contexts:** `stream, server`

Enables the specified protocols for HTTPS connections
with [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).

## `js_fetch_timeout`

**Syntax:** *`time`*

**Default:** 60s

**Contexts:** `stream, server`

Defines a timeout for reading and writing
for [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).
The timeout is set only between two successive read/write operations,
not for the whole response.
If no data is transmitted within this time, the connection is closed.

## `js_fetch_trusted_certificate`

**Syntax:** *`file`*

**Contexts:** `stream, server`

Specifies a *`file`* with trusted CA certificates in the PEM format
used to
[verify](https://nginx.org/en/docs/njs/reference.html#fetch_verify)
the HTTPS certificate
with [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).

## `js_fetch_verify`

**Syntax:** `on` | `off`

**Default:** on

**Contexts:** `stream, server`

Enables or disables verification of the HTTPS server certificate
with [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).

## `js_fetch_verify_depth`

**Syntax:** *`number`*

**Default:** 100

**Contexts:** `stream, server`

Sets the verification depth in the HTTPS server certificates chain
with [Fetch API](https://nginx.org/en/docs/njs/reference.html#ngx_fetch).

## `js_filter`

**Syntax:** *`function`* | *`module.function`*

**Contexts:** `stream, server`

Sets a data filter.
Since [0.4.0](https://nginx.org/en/docs/njs/changes.html#njs0.4.0),
a module function can be referenced.
The filter function is called once at the moment when the stream session reaches
the [content](https://nginx.org/en/docs/stream/stream_processing.html#content_phase) phase.

The filter function is called with the following arguments:
- `s`

    the [Stream Session](https://nginx.org/en/docs/njs/reference.html#stream) object

At this phase, it is possible to perform initialization
or register a callback with
the [`s.on()`](https://nginx.org/en/docs/njs/reference.html#s_on)
method for each incoming data chunk.
The
[`s.off()`](https://nginx.org/en/docs/njs/reference.html#s_off)
method may be used to unregister a callback and stop filtering.

> As the `js_filter` handler
> returns its result immediately, it supports
> only synchronous operations.
> Thus, asynchronous operations such as
> [`ngx.fetch()`](https://nginx.org/en/docs/njs/reference.html#ngx_fetch)
> or
> [`setTimeout()`](https://nginx.org/en/docs/njs/reference.html#settimeout)
> are not supported.

## `js_import`

**Syntax:** *`module.js`* | *`export_name from module.js`*

**Contexts:** `stream, server`

Imports a module that implements location and variable handlers in njs.
The `export_name` is used as a namespace
to access module functions.
If the `export_name` is not specified,
the module name will be used as a namespace.
```
js_import stream.js;
```
Here, the module name `stream` is used as a namespace
while accessing exports.
If the imported module exports `foo()`,
`stream.foo` is used to refer to it.

Several `js_import` directives can be specified.

> The directive can be specified on the
> `server` level
> since [0.7.7](https://nginx.org/en/docs/njs/changes.html#njs0.7.7).

## `js_include`

**Syntax:** *`file`*

**Contexts:** `stream`

Specifies a file that implements server and variable handlers in njs:
```
nginx.conf:
js_include stream.js;
js_set     $js_addr address;
server {
    listen 127.0.0.1:12345;
    return $js_addr;
}

stream.js:
function address(s) {
    return s.remoteAddress;
}
```

The directive was made obsolete in version
[0.4.0](https://nginx.org/en/docs/njs/changes.html#njs0.4.0)
and was removed in version
[0.7.1](https://nginx.org/en/docs/njs/changes.html#njs0.7.1).
The [`js_import`](https://nginx.org/en/docs/stream/ngx_stream_js_module.html#js_import) directive should be used instead.

## `js_path`

**Syntax:** *`path`*

**Contexts:** `stream, server`

Sets an additional path for njs modules.

> The directive can be specified on the
> `server` level
> since [0.7.7](https://nginx.org/en/docs/njs/changes.html#njs0.7.7).

## `js_periodic`

**Syntax:** *`function`* | *`module.function`* [`interval`=*`time`*] [`jitter`=*`number`*] [`worker_affinity`=*`mask`*]

**Contexts:** `server`

Specifies a content handler to run at regular interval.
The handler receives a
[session object](https://nginx.org/en/docs/njs/reference.html#periodic_session)
as its first argument,
it also has access to global objects such as
[ngx](https://nginx.org/en/docs/njs/reference.html#ngx).

The optional `interval` parameter
sets the interval between two consecutive runs,
by default, 5 seconds.

The optional `jitter` parameter sets the time within which
the location content handler will be randomly delayed,
by default, there is no delay.

By default, the `js_handler` is executed on worker process 0.
The optional `worker_affinity` parameter
allows specifying particular worker processes
where the location content handler should be executed.
Each worker process set is represented by a bitmask of allowed worker processes.
The `all` mask allows the handler to be executed
in all worker processes.

Example:
```
example.conf:

location @periodics {
    # to be run at 1 minute intervals in worker process 0
    js_periodic main.handler interval=60s;

    # to be run at 1 minute intervals in all worker processes
    js_periodic main.handler interval=60s worker_affinity=all;

    # to be run at 1 minute intervals in worker processes 1 and 3
    js_periodic main.handler interval=60s worker_affinity=0101;

    resolver 10.0.0.1;
    js_fetch_trusted_certificate /path/to/ISRG_Root_X1.pem;
}

example.js:

async function handler(s) {
    let reply = await ngx.fetch('https://nginx.org/en/docs/njs/');
    let body = await reply.text();

    ngx.log(ngx.INFO, body);
}
```

## `js_preload_object`

**Syntax:** *`name.json`* | *`name`* from *`file.json`*

**Contexts:** `stream, server`

Preloads an
[immutable object](https://nginx.org/en/docs/njs/preload_objects.html)
at configure time.
The `name` is used as a name of the global variable
though which the object is available in njs code.
If the `name` is not specified,
the file name will be used instead.
```
js_preload_object map.json;
```
Here, the `map` is used as a name
while accessing the preloaded object.

Several `js_preload_object` directives can be specified.

## `js_preread`

**Syntax:** *`function`* | *`module.function`*

**Contexts:** `stream, server`

Sets an njs function which will be called at the
[preread](https://nginx.org/en/docs/stream/stream_processing.html#preread_phase) phase.
Since [0.4.0](https://nginx.org/en/docs/njs/changes.html#njs0.4.0),
a module function can be referenced.

The function is called once
at the moment when the stream session reaches the
[preread](https://nginx.org/en/docs/stream/stream_processing.html#preread_phase) phase
for the first time.
The function is called with the following arguments:

- `s`

    the [Stream Session](https://nginx.org/en/docs/njs/reference.html#stream) object

At this phase, it is possible to perform initialization
or register a callback with
the [`s.on()`](https://nginx.org/en/docs/njs/reference.html#s_on)
method
for each incoming data chunk until one of the following methods are called:
[`s.allow()`](https://nginx.org/en/docs/njs/reference.html#s_allow),
[`s.decline()`](https://nginx.org/en/docs/njs/reference.html#s_decline),
[`s.done()`](https://nginx.org/en/docs/njs/reference.html#s_done).
When one of these methods is called,
the stream session switches to the
[next phase](https://nginx.org/en/docs/stream/stream_processing.html)
and all current
[`s.on()`](https://nginx.org/en/docs/njs/reference.html#s_on)
callbacks are dropped.

> As the `js_preread` handler
> returns its result immediately, it supports
> only synchronous callbacks.
> Thus, asynchronous callbacks such as
> [`ngx.fetch()`](https://nginx.org/en/docs/njs/reference.html#ngx_fetch)
> or
> [`setTimeout()`](https://nginx.org/en/docs/njs/reference.html#settimeout)
> are not supported.
> Nevertheless, asynchronous operations are supported in
> [`s.on()`](https://nginx.org/en/docs/njs/reference.html#s_on)
> callbacks in the
> [preread](https://nginx.org/en/docs/stream/stream_processing.html#preread_phase) phase.
> See
> [this example](https://github.com/nginx/njs-examples#authorizing-connections-using-ngx-fetch-as-auth-request-stream-auth-request) for more information.

## `js_set`

**Syntax:** *`$variable`* *`function`* | *`module.function`* [`nocache`]

**Contexts:** `stream, server`

Sets an njs `function`
for the specified `variable`.
Since [0.4.0](https://nginx.org/en/docs/njs/changes.html#njs0.4.0),
a module function can be referenced.

The function is called when
the variable is referenced for the first time for a given request.
The exact moment depends on a
[phase](https://nginx.org/en/docs/stream/stream_processing.html)
at which the variable is referenced.
This can be used to perform some logic
not related to variable evaluation.
For example, if the variable is referenced only in the
[`log_format`](https://nginx.org/en/docs/stream/ngx_stream_log_module.html#log_format) directive,
its handler will not be executed until the log phase.
This handler can be used to do some cleanup
right before the request is freed.

Since [0.8.6](https://nginx.org/en/docs/njs/changes.html#njs0.8.6), when
optional argument `nocache` is provided the handler
is called every time it is referenced.
Due to current limitations
of the [rewrite](https://nginx.org/en/docs/stream/ngx_stream_js_module.html) module,
when a `nocache` variable is referenced by the
[set](https://nginx.org/en/docs/stream/ngx_stream_set_module.html#set) directive
its handler should always return a fixed-length value.

> As the `js_set` handler
> returns its result immediately, it supports
> only synchronous callbacks.
> Thus, asynchronous callbacks such as
> [ngx.fetch()](https://nginx.org/en/docs/njs/reference.html#ngx_fetch)
> or
> [setTimeout()](https://nginx.org/en/docs/njs/reference.html#settimeout)
> are not supported.

> The directive can be specified on the
> `server` level
> since [0.7.7](https://nginx.org/en/docs/njs/changes.html#njs0.7.7).

## `js_shared_dict_zone`

**Syntax:** `zone`=*`name`*:*`size`* [`timeout`=*`time`*] [`type`=`string`|`number`] [`evict`]

**Contexts:** `stream`

Sets the *`name`* and *`size`* of the shared memory zone
that keeps the
key-value [dictionary](https://nginx.org/en/docs/njs/reference.html#dict)
shared between worker processes.

By default the shared dictionary uses a string as a key and a value.
The optional `type` parameter
allows redefining the value type to number.

The optional `timeout` parameter sets
the time in milliseconds
after which all shared dictionary entries are removed from the zone.
If some entries require a different removal time, it can be set
with the `timeout` argument of the
[add](https://nginx.org/en/docs/njs/reference.html#dict_add),
[incr](https://nginx.org/en/docs/njs/reference.html#dict_incr), and
[set](https://nginx.org/en/docs/njs/reference.html#dict_set)
methods
([0.8.5](https://nginx.org/en/docs/njs/changes.html#njs0.8.5)).

The optional `evict` parameter removes the oldest
key-value pair when the zone storage is exhausted.

Example:
```
example.conf:
    # Creates a 1Mb dictionary with string values,
    # removes key-value pairs after 60 seconds of inactivity:
    js_shared_dict_zone zone=foo:1M timeout=60s;

    # Creates a 512Kb dictionary with string values,
    # forcibly removes oldest key-value pairs when the zone is exhausted:
    js_shared_dict_zone zone=bar:512K timeout=30s evict;

    # Creates a 32Kb permanent dictionary with number values:
    js_shared_dict_zone zone=num:32k type=number;

example.js:
    function get(r) {
        r.return(200, ngx.shared.foo.get(r.args.key));
    }

    function set(r) {
        r.return(200, ngx.shared.foo.set(r.args.key, r.args.value));
    }

    function del(r) {
        r.return(200, ngx.shared.bar.delete(r.args.key));
    }

    function increment(r) {
        r.return(200, ngx.shared.num.incr(r.args.key, 2));
    }
```

## `js_var`

**Syntax:** *`$variable`* [*`value`*]

**Contexts:** `stream, server`

Declares
a [writable](https://nginx.org/en/docs/njs/reference.html#r_variables)
variable.
The value can contain text, variables, and their combination.

> The directive can be specified on the
> `server` level
> since [0.7.7](https://nginx.org/en/docs/njs/changes.html#njs0.7.7).

