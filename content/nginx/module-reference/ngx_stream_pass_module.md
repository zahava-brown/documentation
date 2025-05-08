---
title: "ngx_stream_pass_module"
id: "/en/docs/stream/ngx_stream_pass_module.html"
toc: true
---

## `pass`

**Syntax:** *`address`*

**Contexts:** `server`

Sets server address to pass client connection to.
The address can be specified as an IP address
and a port:
```
pass 127.0.0.1:12345;
```
or as a UNIX-domain socket path:
```
pass unix:/tmp/stream.socket;
```

The address can also be specified using variables:
```
pass $upstream;
```

