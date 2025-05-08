---
title: "ngx_http_perl_module"
id: "/en/docs/http/ngx_http_perl_module.html"
toc: true
---

## `perl`

**Syntax:** *`module`*::*`function`*|'sub { ... }'

**Contexts:** `location, limit_except`

Sets a Perl handler for the given location.

## `perl_modules`

**Syntax:** *`path`*

**Contexts:** `http`

Sets an additional path for Perl modules.

## `perl_require`

**Syntax:** *`module`*

**Contexts:** `http`

Defines the name of a module that will be loaded during each
reconfiguration.
Several `perl_require` directives can be present.

## `perl_set`

**Syntax:** *`$variable`* *`module`*::*`function`*|'sub { ... }'

**Contexts:** `http`

Installs a Perl handler for the specified variable.

