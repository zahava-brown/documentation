---
title: "ngx_stream_geoip_module"
id: "/en/docs/stream/ngx_stream_geoip_module.html"
toc: true
---

## `geoip_country`

**Syntax:** *`file`*

**Contexts:** `stream`

Specifies a database used to determine the country
depending on the client IP address.
The following variables are available when using this database:
- `$geoip_country_code`

    two-letter country code, for example,
    “`RU`”, “`US`”.
- `$geoip_country_code3`

    three-letter country code, for example,
    “`RUS`”, “`USA`”.
- `$geoip_country_name`

    country name, for example,
    “`Russian Federation`”, “`United States`”.

## `geoip_city`

**Syntax:** *`file`*

**Contexts:** `stream`

Specifies a database used to determine the country, region, and city
depending on the client IP address.
The following variables are available when using this database:
- `$geoip_area_code`

    telephone area code (US only).
    > This variable may contain outdated information since
    > the corresponding database field is deprecated.
- `$geoip_city_continent_code`

    two-letter continent code, for example,
    “`EU`”, “`NA`”.
- `$geoip_city_country_code`

    two-letter country code, for example,
    “`RU`”, “`US`”.
- `$geoip_city_country_code3`

    three-letter country code, for example,
    “`RUS`”, “`USA`”.
- `$geoip_city_country_name`

    country name, for example,
    “`Russian Federation`”, “`United States`”.
- `$geoip_dma_code`

    DMA region code in US (also known as “metro code”), according to the
    [geotargeting](https://developers.google.com/adwords/api/docs/appendix/cities-DMAregions)
    in Google AdWords API.
- `$geoip_latitude`

    latitude.
- `$geoip_longitude`

    longitude.
- `$geoip_region`

    two-symbol country region code (region, territory, state, province, federal land
    and the like), for example,
    “`48`”, “`DC`”.
- `$geoip_region_name`

    country region name (region, territory, state, province, federal land
    and the like), for example,
    “`Moscow City`”, “`District of Columbia`”.
- `$geoip_city`

    city name, for example,
    “`Moscow`”, “`Washington`”.
- `$geoip_postal_code`

    postal code.

## `geoip_org`

**Syntax:** *`file`*

**Contexts:** `stream`

Specifies a database used to determine the organization
depending on the client IP address.
The following variable is available when using this database:
- `$geoip_org`

    organization name, for example, “The University of Melbourne”.

