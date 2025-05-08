---
title: "ngx_mail_realip_module"
id: "/en/docs/mail/ngx_mail_realip_module.html"
toc: true
---

## `set_real_ip_from`

**Syntax:** *`address`* | *`CIDR`* | `unix:`

**Contexts:** `mail, server`

Defines trusted addresses that are known to send correct
replacement addresses.
If the special value `unix:` is specified,
all UNIX-domain sockets will be trusted.

