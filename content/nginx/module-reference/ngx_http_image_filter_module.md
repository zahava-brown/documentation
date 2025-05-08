---
title: "ngx_http_image_filter_module"
id: "/en/docs/http/ngx_http_image_filter_module.html"
toc: true
---

## `image_filter`

**Syntax:** `off`

**Default:** off

**Contexts:** `location`

Sets the type of transformation to perform on images:
- `off`

    turns off module processing in a surrounding location.
- `test`

    ensures that responses are images in either JPEG, GIF, PNG, or WebP format.
    Otherwise, the
    415 (Unsupported Media Type)
    error is returned.
- `size`

    outputs information about images in a JSON format, e.g.:
    ```
    { "img" : { "width": 100, "height": 100, "type": "gif" } }
    ```
    In case of an error, the output is as follows:
    ```
    {}
    ```
- `rotate`
`90`|`180`|`270`

    rotates images counter-clockwise by the specified number of degrees.
    Parameter value can contain variables.
    This mode can be used either alone or along with the
    `resize` and `crop` transformations.
- `resize`
*`width`*
*`height`*

    proportionally reduces an image to the specified sizes.
    To reduce by only one dimension, another dimension can be specified as
    “`-`”.
    In case of an error, the server will return code
    415 (Unsupported Media Type).
    Parameter values can contain variables.
    When used along with the `rotate` parameter,
    the rotation happens after reduction.
- `crop`
*`width`*
*`height`*

    proportionally reduces an image to the larger side size
    and crops extraneous edges by another side.
    To reduce by only one dimension, another dimension can be specified as
    “`-`”.
    In case of an error, the server will return code
    415 (Unsupported Media Type).
    Parameter values can contain variables.
    When used along with the `rotate` parameter,
    the rotation happens before reduction.

## `image_filter_buffer`

**Syntax:** *`size`*

**Default:** 1M

**Contexts:** `http, server, location`

Sets the maximum size of the buffer used for reading images.
When the size is exceeded the server returns error
415 (Unsupported Media Type).

## `image_filter_interlace`

**Syntax:** `on` | `off`

**Default:** off

**Contexts:** `http, server, location`

If enabled, final images will be interlaced.
For JPEG, final images will be in “progressive JPEG” format.

## `image_filter_jpeg_quality`

**Syntax:** *`quality`*

**Default:** 75

**Contexts:** `http, server, location`

Sets the desired *`quality`* of the transformed JPEG images.
Acceptable values are in the range from 1 to 100.
Lesser values usually imply both lower image quality and less data to transfer.
The maximum recommended value is 95.
Parameter value can contain variables.

## `image_filter_sharpen`

**Syntax:** *`percent`*

**Default:** 0

**Contexts:** `http, server, location`

Increases sharpness of the final image.
The sharpness percentage can exceed 100.
The zero value disables sharpening.
Parameter value can contain variables.

## `image_filter_transparency`

**Syntax:** `on`|`off`

**Default:** on

**Contexts:** `http, server, location`

Defines whether transparency should be preserved when transforming
GIF images or PNG images with colors specified by a palette.
The loss of transparency results in images of a better quality.
The alpha channel transparency in PNG is always preserved.

## `image_filter_webp_quality`

**Syntax:** *`quality`*

**Default:** 80

**Contexts:** `http, server, location`

Sets the desired *`quality`* of the transformed WebP images.
Acceptable values are in the range from 1 to 100.
Lesser values usually imply both lower image quality and less data to transfer.
Parameter value can contain variables.

