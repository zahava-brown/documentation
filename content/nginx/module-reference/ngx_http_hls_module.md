---
title: "ngx_http_hls_module"
id: "/en/docs/http/ngx_http_hls_module.html"
toc: true
---

## `hls`

**Contexts:** `location`

Turns on HLS streaming in the surrounding location.

## `hls_buffers`

**Syntax:** *`number`* *`size`*

**Default:** 8 2m

**Contexts:** `http, server, location`

Sets the maximum *`number`* and *`size`* of buffers
that are used for reading and writing data frames.

## `hls_forward_args`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location`

Adds arguments from a playlist request to URIs of fragments.
This may be useful for performing client authorization at the moment of
requesting a fragment, or when protecting an HLS stream with the
[ngx_http_secure_link_module](https://nginx.org/en/docs/http/ngx_http_secure_link_module.html)
module.

For example, if a client requests a playlist
`http://example.com/hls/test.mp4.m3u8?a=1&b=2`,
the arguments `a=1` and `b=2`
will be added to URIs of fragments after the arguments
`start` and `end`:
```
#EXTM3U
#EXT-X-VERSION:3
#EXT-X-TARGETDURATION:15
#EXT-X-PLAYLIST-TYPE:VOD

#EXTINF:9.333,
test.mp4.ts?start=0.000&end=9.333&a=1&b=2
#EXTINF:7.167,
test.mp4.ts?start=9.333&end=16.500&a=1&b=2
#EXTINF:5.416,
test.mp4.ts?start=16.500&end=21.916&a=1&b=2
#EXTINF:5.500,
test.mp4.ts?start=21.916&end=27.416&a=1&b=2
#EXTINF:15.167,
test.mp4.ts?start=27.416&end=42.583&a=1&b=2
#EXTINF:9.626,
test.mp4.ts?start=42.583&end=52.209&a=1&b=2

#EXT-X-ENDLIST
```

If an HLS stream is protected with the
[ngx_http_secure_link_module](https://nginx.org/en/docs/http/ngx_http_secure_link_module.html)
module, `$uri` should not be used in the
[`secure_link_md5`](https://nginx.org/en/docs/http/ngx_http_secure_link_module.html#secure_link_md5)
expression because this will cause errors when requesting the fragments.
[Base URI](https://nginx.org/en/docs/http/ngx_http_map_module.html#map) should be used
instead of `$uri`
(`$hls_uri` in the example):
```
http {
    ...

    map $uri $hls_uri {
        ~^(?<base_uri>.*).m3u8$ $base_uri;
        ~^(?<base_uri>.*).ts$   $base_uri;
        default                 $uri;
    }

    server {
        ...

        location /hls/ {
            hls;
            hls_forward_args on;

            alias /var/videos/;

            secure_link $arg_md5,$arg_expires;
            secure_link_md5 "$secure_link_expires$hls_uri$remote_addr secret";

            if ($secure_link = "") {
                return 403;
            }

            if ($secure_link = "0") {
                return 410;
            }
        }
    }
}
```

## `hls_fragment`

**Syntax:** *`time`*

**Default:** 5s

**Contexts:** `http, server, location`

Defines the default fragment length for playlist URIs requested without the
“`len`” argument.

## `hls_mp4_buffer_size`

**Syntax:** *`size`*

**Default:** 512k

**Contexts:** `http, server, location`

Sets the initial *`size`* of the buffer used for
processing MP4 and MOV files.

## `hls_mp4_max_buffer_size`

**Syntax:** *`size`*

**Default:** 10m

**Contexts:** `http, server, location`

During metadata processing, a larger buffer may become necessary.
Its size cannot exceed the specified *`size`*,
or else nginx will return the server error
500 (Internal Server Error),
and log the following message:
```
"/some/movie/file.mp4" mp4 moov atom is too large:
12583268, you may want to increase hls_mp4_max_buffer_size
```

