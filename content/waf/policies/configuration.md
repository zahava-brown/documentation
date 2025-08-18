---
# We use sentence case and present imperative tone
title: "Configure policies"
# Weights are assigned in increments of 100: determines sorting order
weight: 100
# Creates a table of contents and sidebar, useful for large documents
toc: true
# Types have a 1:1 relationship with Hugo archetypes, so you shouldn't need to change this
nd-content-type: how-to
# Intended for internal catalogue and search, case sensitive:
# Agent, N4Azure, NIC, NIM, NGF, NAP-DOS, NAP-WAF, NGINX One, NGINX+, Solutions, Unit
nd-product: NAP-WAF
---

{{< call-out "warning" "Information architecture note" >}}

The design intention for this page is to as a single source of truth to replace the two [Configuration]({{< ref "/nap-waf/v4/configuration-guide/configuration.md" >}}) [Guides]({{< ref "/nap-waf/v5/configuration-guide/configuration.md" >}}) (two separate links).

Outside of the overlapping information for Policy configuration, the existing pages also include general configuration information, such as for F5 WAF for NGINX itself. This detail can be added to a separate page, ensuring that each document acts as a solution for exactly one problem at a time.

{{</ call-out >}}

This page describes the security features available with F5 WAF for NGINX and how to enable them. 

For better understanding of some contextual nouns, read the [Terminology]({{< ref "/waf/fundamentals/terminology.md" >}}) topic.

## General configuration

F5 WAF for NGINX ships with two reference policies, both with a default enforcement mode set to Blocking:

- The **default** policy which is identical to the base template and provides OWASP Top 10 and Bot security protection out of the box.
- The **strict** policy contains more restrictive criteria for blocking traffic than the default policy. It is meant to be used for protecting sensitive applications that require more security but with higher risk of false positives.

You can use these policies as-is, but they are often the starting points for customizations according to the needs of the applications F5 WAF NGINX protects.

### Configuration overview

The F5 WAF for NGINX security policy configuration uses a declarative format based on a pre-defined base template. 

The policy is represented in a JSON file which you can edit to add, modify and remove security capabilities in reference to the base template. 

The way the policy is integrated into the NGINX configuration is through referencing the JSON file (Using the full path) in the `nginx.conf` file.

{{< call-out "note" >}}

F5 WAF for NGINX provides a [JSON Schema](https://json-schema.org/) which can be used to validate a JSON policy file for format compliance. 

The schema file can be generated using a script once F5 WAF for NGINX is installed: `sudo /opt/app_protect/bin/generate_json_schema.pl`. 

This script will output the schema to a file named `policy.json` into the current working directory. Once the schema file is generated, you can use validation tools such as [AJV](https://ajv.js.org/standalone.html) to validate a JSON policy file.

{{< /call-out >}}

In the following example, the NGINX configuration file with F5 WAF for NGINX is enabled in the HTTP context and the policy _/etc/app_protect/conf/NginxDefaultPolicy.json_ is used:

```nginx
user nginx;
worker_processes  4;

load_module modules/ngx_http_app_protect_module.so;

error_log /var/log/nginx/error.log debug;

events {
    worker_connections  65536;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;

    app_protect_enable on; # This is how you enable F5 WAF for NGINX in the relevant context/block
    app_protect_policy_file "/etc/app_protect/conf/NginxDefaultPolicy.json"; # This refers to which policy file to use, which falls back to the default policy
    app_protect_security_log_enable on; # This section enables logging
    app_protect_security_log "/etc/app_protect/conf/log_default.json" syslog:server=127.0.0.1:514; # This is where the remote logger is defined in terms of: logging options (defined in the referenced file), log server IP, log server port

    server {
        listen       80;
        server_name  localhost;
        proxy_http_version 1.1;

        location / {
            client_max_body_size 0;
            default_type text/html;
            proxy_pass http://172.29.38.211:80$request_uri;
        }
    }
}
```

### Base template

The base template is the common starting point for any policy you write.

The default policy reflects the base template without any further modifications, so the terms _base template_ and _default policy_ are used interchangeably. 

The default policy appears as follows:

```json
{
    "policy" : {
        "name": "app_protect_default_policy",
        "template": { "name": "POLICY_TEMPLATE_NGINX_BASE" }
    }
}
```

The default policy enforces violations by **Violation Rating**, the F5 WAF for NGINX computed assessment of the risk of the request based on the triggered violations.

- 0: No violation
- 1-2: False positive
- 3: Needs examination
- 4-5: Threat

The default policy enables most of the violations and signature sets with Alarm turned **ON**, but not **Block**. 

These violations and signatures, when detected in a request, affect the violation rating. By default, if the violation rating is calculated to be malicious (4-5) the request will be blocked by the `VIOL_RATING_THREAT` violation. 

This is true even if the other violations and signatures detected in that request have the Block flag turned OFF. It is the `VIOL_RATING_THREAT` violation having the Block flag turned ON that caused the blocking, but indirectly the combination of all the other violations and signatures in Alarm caused the request to be blocked. 

By default, other requests which have a lower violation rating are not blocked, except for some specific violations described below. This is to minimize false positives. However, you can change the default behavior. 

For example, if you want to add blocking on a violation rating of 3 as well, enable blocking for the `VIOL_RATING_NEED_EXAMINATION` violation.

The following violations and signature sets have a low chance of being false positives and are, therefore, configured by default to block the request regardless of its Violation Rating:
- High accuracy attack signatures
- Threat campaigns
- Malformed request: unparsable header, malformed cookie and malformed body (JSON or XML).

### Default policy

F5 WAF for NGINX offers prebuilt bundles for security policies:

- _app_protect_default_policy_
- _app_protect_strict_policy_

{{< call-out "important" >}}

You cannot mix these prebuilt bundles with custom policy bundles within the same `nginx.conf` file.

{{< /call-out >}}

Example:

```nginx
    ...
    location / {

        # F5 WAF for NGINX
        app_protect_enable on;
        app_protect_policy_file app_protect_strict_policy;
        app_protect_security_log_enable on;
        app_protect_security_log log_all stderr;

        proxy_pass http://127.0.0.1:8080/;
    }
```

#### Updating default policy bundles

{{< call-out "note" >}}

This section assumes that you have built a [compiler image]({{< ref "/waf/tools/compiler.md" >}}) named `waf-compiler-1.0.0:custom`.

{{< /call-out >}}

To generate versions of the default policies that include the latest security updates, use the `-factory-policy` option instead of a source policy file.

For instance, to create an updated version of the `app_protect_default_policy`, use the following command:

```shell
docker run \
 -v $(pwd):$(pwd) \
 waf-compiler-1.0.0:custom \
 -factory-policy default -o $(pwd)/new_default_policy.tgz
```

To create an updated version of the `app_protect_strict_policy`, use:

```shell
docker run \
 -v $(pwd):$(pwd) \
 waf-compiler-1.0.0:custom \
 -factory-policy strict -o $(pwd)/new_strict_policy.tgz
```

After creating the updated version of a policy, reference it in the `nginx.conf` file:

```nginx
app_protect_policy_file /policies_mount/new_default_policy.tgz;
```

### Strict policy

The strict policy is recommended as a starting point for applications requiring a higher level of security. 

Similar to policies, it is customized from the base template, so it detects and blocks everything the default policy does.

To obtain the strict policy, execute the following command:

```shell
sudo docker run --rm -v $(pwd):$(pwd) --entrypoint='' private-registry.nginx.com/nap/waf-compiler:1.0.0 cat /etc/app_protect/conf/NginxStrictPolicy.json
```

Replace the `1.0.0` with the actual release version.

In addition the strict policy also **blocks** the following:

- Requests that have a Violation Rating of 3, "Needs examination". This occurs because the `VIOL_RATING_NEED_EXAMINATION` violation's block flag is enabled in the strict policy.
- Requests with the `VIOL_EVASION` violation (evasion techniques).
- Requests with violations that restrict options in the request and response: HTTP method, response status code and disallowed file types.

{{< call-out "note" >}} 

Other violations, specifically attack signatures and metacharacters, which are more prone to false positives, still have only Alarm turned on, without blocking, contributing to the Violation Rating as in the Default policy.

{{< /call-out >}}

In addition, the Strict policy also enables the following features in **alarm only** mode:

- **Data Guard**: masking Credit Card Number (CCN), US Social Security Number (SSN) and custom patterns found in HTTP responses.
- **HTTP response data leakage signatures**: preventing exfiltration of sensitive information from the servers.
- **More restrictive limitations**: mainly sizing and parsing of JSON and XML payloads.
- **Cookie attribute insertion**: the Strict policy adds the **Secure** and **SameSite=lax** attributes to every cookie set by the application server. These attributes are enforced by the browsers and protect against session hijacking and CSRF attacks respectively.

## Supported security policy features

{{< include "waf/supported-policy-features.md" >}}

## Attack signatures

Attack signatures are rules or patterns that identify attack sequences or classes of attacks on a web application and its components. You can apply attack signatures to both requests and responses. F5 WAF for NGINX includes predefined attack signatures to protect your application against all attack types identified by the system.

As new attack signatures are identified, they will become available for download and installation so that your system will always have the most up-to-date protection. You can update the attack signatures without updating F5 WAF for NGINX, and conversely, you can update F5 WAF for NGINX without changing the attack signature package, unless you upgrade to a new NGINX Plus release.

### Signature settings

| Setting | JSON property | F5 WAF for NGINX support | Default value |
| --------| ------------- | ------------------------ | ------------- |
| Signature sets | signature-sets | All available sets. | See signature set list below |
| Signatures | signatures | "Enabled" flag can be modified. | All signatures in the included sets are enabled. |
| Auto-Added signature accuracy | minimumAccuracyForAutoAddedSignatures | Editable | Medium |

### Signature sets

The default and strict policies include and enable common signature sets, which are categorized groups of [signatures](#attack-signatures-overview) applied to the policy. However, you may wish to modify the list of signature sets and their logging and enforcement settings via the `signature-sets` array property. There are several ways to configure the enforced signature sets.

One way is by use of the `All Signatures` signature set, which is simply a predefined signature set that includes all signatures known to NGINX App Protect WAF.

In this example, the `All Signatures` set (and therefore the signatures included within) are configured to be enforced and logged respectively, by setting their `block` and `alarm` properties:

```json
{
    "policy": {
        "name": "attack_sigs",
        "template": { "name": "POLICY_TEMPLATE_NGINX_BASE" },
        "applicationLanguage": "utf-8",
        "enforcementMode": "blocking",
        "signature-sets": [
            {
                 "name": "All Signatures",
                 "block": true,
                 "alarm": true
            }
        ]
    }
}
```

In this example, only high accuracy signatures are configured to be enforced, but SQL Injection signatures are detected and reported:

```json
{
    "policy": {
        "name": "attack_sigs",
        "template": {
            "name": "POLICY_TEMPLATE_NGINX_BASE"
        },
        "applicationLanguage": "utf-8",
        "enforcementMode": "blocking",
        "signature-sets": [
            {
                "name": "High Accuracy Signatures",
                "block": true,
                "alarm": true
            },
            {
                "name": "SQL Injection Signatures",
                "block": false,
                "alarm": true
            }
        ]
    }
}
```

Since the "All Signatures" set is not included in the default policy, turning OFF both alarm and block has no effect because all the other sets with alarm turned ON (and high accuracy signatures with block enabled) are still in place and a signature that is a member of multiple sets behaves in accordance with the strict settings of all sets it belongs to. The only way to remove signature sets is to remove or disable sets that are part of the [default policy](#signature-sets-in-default-policy).

For example, in the below default policy, even though All Signature's Alarm/Block settings are set to false, we cannot ignore all attack signatures enforcement as some of the signature sets will be enabled in their strict policy. If the end users want to remove a specific signature set then they must explicitly mention it under the [strict policy](#the-strict-policy).

```json
{
    "policy": {
        "name": "signatures_block",
        "template": {
            "name": "POLICY_TEMPLATE_NGINX_BASE"
        },
        "applicationLanguage": "utf-8",
        "caseInsensitive": false,
        "enforcementMode": "blocking",
        "signature-sets": [
            {
                "name": "Generic Detection Signatures (High/Medium Accuracy)",
                "block": false,
                "alarm": false
            },
            {
                "name": "All Signatures",
                "block": false,
                "alarm": false
            }
        ]
    }
}
```

A signature may belong to more than one set in the policy. Its behavior is determined by the most severe action across all the sets that contain it. In the above example, a high accuracy SQL injection signature will both alarm and block, because the `High Accuracy Signatures` set is blocking and both sets trigger alarm.

The default policy already includes many signature sets, most of which are determined by the attack type these signatures protect from, for example `Cross-Site Scripting Signatures` or `SQL Injection Signatures`. See [the full list](#signature-sets-in-default-policy) above. In some cases you may want to exclude individual signatures.

In this example, signature ID 200001834 is excluded from enforcement:

```json
{
    "policy": {
        "name": "signature_exclude",
        "template": { "name": "POLICY_TEMPLATE_NGINX_BASE" },
        "applicationLanguage": "utf-8",
        "enforcementMode": "blocking",
        "signature-sets": [
            {
                 "name": "All Signatures",
                 "block": true,
                 "alarm": true
            }
        ],
        "signatures": [
            {
                 "signatureId": 200001834,
                 "enabled": false
            }
        ]
    }
}
```

Another way to exclude signature ID 200001834 is by using the `modifications` section instead of the `signatures` section used in the example above:

```json
{
    "policy": {
        "name": "signature_modification_entitytype",
        "template": { "name": "POLICY_TEMPLATE_NGINX_BASE" },
        "applicationLanguage": "utf-8",
        "enforcementMode": "blocking",
        "signature-sets": [
            {
                 "name": "All Signatures",
                 "block": true,
                 "alarm": true
            }
        ]
    },
    "modifications": [
        {
            "entityChanges": {
                "enabled": false
            },
            "entity": {
                "signatureId": 200001834
            },
            "entityType": "signature",
            "action": "add-or-update"
        }
    ]
}
```

To exclude multiple attack signatures, each signature ID needs to be added as a separate entity under the `modifications` list:

```json
{
    "modifications": [
        {
            "entityChanges": {
                "enabled": false
            },
            "entity": {
                "signatureId": 200001834
            },
            "entityType": "signature",
            "action": "add-or-update"
        },
        {
            "entityChanges": {
                "enabled": false
            },
            "entity": {
                "signatureId": 200004461
            },
            "entityType": "signature",
            "action": "add-or-update"
        }
    ]
}
```

In the above examples, the signatures were disabled for all the requests that are inspected by the respective policy. You can also exclude signatures for specific URLs or parameters, while still enable them for the other URLs and parameters. See the sections on [User-Defined URLs](#user-defined-urls) and [User-Defined Parameters](#user-defined-parameters) for details.

In some cases, you may want to remove a whole signature set that was included in the default policy. For example, suppose your protected application does not use XML and hence is not exposed to XPath injection. You would like to remove the set `XPath Injection Signatures`. There are two ways to do that. The first is to set the `alarm` and `block` flags to `false` for this signature set overriding the settings in the base template:

```json
{
    "policy": {
        "name": "no_xpath_policy",
        "template": { "name": "POLICY_TEMPLATE_NGINX_BASE" },
        "signature-sets": [
            {
                 "name": "XPath Injection Signatures",
                 "block": false,
                 "alarm": false
            }
        ]
    }
}
```

The second way is to remove this set totally from the policy using the `$action` meta-property.

```json
{
    "policy": {
        "name": "no_xpath_policy",
        "template": { "name": "POLICY_TEMPLATE_NGINX_BASE" },
        "signature-sets": [
            {
                 "name": "XPath Injection Signatures",
                 "$action": "delete"
            }
        ]
    }
}
```

Although the two methods are functionally equivalent, the second one is preferable for performance reasons.

The following signature sets are included in the default policy. Most of the sets are defined by the Attack Type they protect from. In all sets the **Alarm** flag is enabled and **Block** disabled except High Accuracy Signatures, which are set to **blocked** (`block` parameter is enabled).

- Command Execution Signatures
- Cross Site Scripting Signatures
- Directory Indexing Signatures
- Information Leakage Signatures
- OS Command Injection Signatures
- Path Traversal Signatures
- Predictable Resource Location Signatures
- Remote File Include Signatures
- SQL Injection Signatures
- Authentication/Authorization Attack Signatures
- XML External Entities (XXE) Signatures
- XPath Injection Signatures
- Buffer Overflow Signatures
- Denial of Service Signatures
- Vulnerability Scan Signatures
- High Accuracy Signatures
- Server Side Code Injection Signatures
- CVE Signatures

These signatures sets are included but are not part of the default template.

-   All Response Signatures
-   All Signatures
-   Generic Detection Signatures
-   Generic Detection Signatures (High Accuracy)
-   Generic Detection Signatures (High/Medium Accuracy)
-   High Accuracy Signatures
-   Low Accuracy Signatures
-   Medium Accuracy Signatures
-   OWA Signatures
-   WebSphere signatures
-   HTTP Response Splitting Signatures
-   Other Application Attacks Signatures
-   High Accuracy Detection Evasion Signatures