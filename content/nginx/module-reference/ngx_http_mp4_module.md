---
title: "ngx_http_mp4_module"
id: "/en/docs/http/ngx_http_mp4_module.html"
toc: true
---

## `mp4`

**Contexts:** `location`

Turns on module processing in a surrounding location.

## `mp4_buffer_size`

**Syntax:** *`size`*

**Default:** 512K

**Contexts:** `http, server, location`

Sets the initial *`size`* of the buffer used for
processing MP4 files.

## `mp4_max_buffer_size`

**Syntax:** *`size`*

**Default:** 10M

**Contexts:** `http, server, location`

During metadata processing, a larger buffer may become necessary.
Its size cannot exceed the specified *`size`*,
or else nginx will return the
500 (Internal Server Error) server error,
and log the following message:
```
"/some/movie/file.mp4" mp4 moov atom is too large:
12583268, you may want to increase mp4_max_buffer_size
```

## `mp4_limit_rate`

**Syntax:** `on` | `off` | *`factor`*

**Default:** off

**Contexts:** `http, server, location`

Limits the rate of response transmission to a client.
The rate is limited based on the average bitrate of the
MP4 file served.
To calculate the rate, the bitrate is multiplied by the specified
*`factor`*.
The special value “`on`” corresponds to the factor of 1.1.
The special value “`off`” disables rate limiting.
The limit is set per a request, and so if a client simultaneously opens
two connections, the overall rate will be twice as much
as the specified limit.

> This directive is available as part of our
> [commercial subscription](https://nginx.com/products/).

## `mp4_limit_rate_after`

**Syntax:** *`time`*

**Default:** 60s

**Contexts:** `http, server, location`

Sets the initial amount of media data (measured in playback time)
after which the further transmission of the response to a client
will be rate limited.

> This directive is available as part of our
> [commercial subscription](https://nginx.com/products/).

## `mp4_start_key_frame`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location`

Forces output video to always start with a key video frame.
If the `start` argument does not point to a key frame,
initial frames are hidden using an mp4 edit list.
Edit lists are supported by major players and browsers such as
Chrome, Safari, QuickTime and ffmpeg,
partially supported by Firefox.

