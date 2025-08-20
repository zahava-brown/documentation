---
description: Ensure long-term data transmission security. Prepare for future changes in cryptography.
type:
- task
title: Set up post-quantum cryptography
toc: true
weight: 560
product: NGINX-PLUS
nd-docs: DOCS-0000
---

# Enable post-quantum cryptography

Post-quantum cryptography (PQC) helps protect encrypted traffic against future quantum attacks that could break algorithms such as RSA and ECC. NGINX Plus, starting with R33, provides **PQC support** through the Open Quantum Safe (OQS) provider for OpenSSL 3.x. Use this guide to test PQC ciphers and certificates.

{{< call-out "important" >}}
This feature targets evaluation and lab environments. PQC identifiers and provider behavior can change. Validate thoroughly before any production use.
{{< /call-out >}}

According to the OpenSSL foundation, OpenSSL version 3.5 

## How PQC works with NGINX Plus

- **OpenSSL 3.x providers** — NGINX Plus uses OpenSSL 3.x, which can dynamically load the **oqs-provider** to add PQC algorithms for TLS.
- **Algorithms** — The OQS provider exposes NIST-selected/finalized PQC algorithms (for example, **Kyber/ML-KEM** for key exchange and **Dilithium/ML-DSA** for signatures).
- **Hybrid TLS** — You can test hybrid key exchange groups that combine classical and quantum-safe methods.

PQC is a 

## Prerequisites

- NGINX Plus **R33** (or later)
  - OpenSSL 3.x is the default for Ubuntu 22.04 and above
- `sudo` privileges and outbound internet access for build dependencies.
- The ability to build from source and edit configuration files.

## Debian-based distributions

These distributions include Ubuntu 22.04 and above.

### Install build dependencies

```bash
sudo apt update
sudo apt install -y build-essential git cmake ninja-build libssl-dev pkg-config
```

### Build and install `liboqs`

You need to build and install `liboqs`, an open source C library for quantum-safe cryptographic algorithms.

```bash
git clone --branch main https://github.com/open-quantum-safe/liboqs.git
cd liboqs
mkdir build && cd build
cmake -GNinja -DCMAKE_INSTALL_PREFIX=/usr/local -DOQS_DIST_BUILD=ON ..
ninja
sudo ninja install
```

### Build and install `oqs-provider`

You need to build and install the `oqs-provider`, which enables QSC for OpenSSL 3.x.

```bash
git clone --branch main https://github.com/open-quantum-safe/oqs-provider.git
cd oqs-provider
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local -DOPENSSL_ROOT_DIR=/usr/local/ssl ..
make -j"$(nproc)"
sudo make install
```

### Build and install OpenSSL 3 with local prefix

You can 


```bash
git clone https://github.com/openssl/openssl.git
cd openssl
./Configure --prefix=/usr/local/ssl --openssldir=/usr/local/ssl linux-x86_64
make -j"$(nproc)"
sudo make install_sw
```

> **Note**
>
> You are installing OpenSSL to **/usr/local/ssl** (side-by-side). NGINX Plus continues to use the system OpenSSL it was linked against, but it can still load providers if directed to the correct `openssl.cnf`. You will point both the `openssl` CLI and the NGINX master process at the same config file in the next steps.

## Step 5: Enable the OQS provider in OpenSSL

Create or edit `/usr/local/ssl/openssl.cnf`:

```ini
openssl_conf = openssl_init

[openssl_init]
providers = provider_sect

[provider_sect]
default = default_sect
oqsprovider = oqsprovider_sect

[default_sect]
activate = 1

[oqsprovider_sect]
activate = 1
```

Export the config path for your shell session:

```bash
export OPENSSL_CONF=/usr/local/ssl/openssl.cnf
```

Verify the provider loads:

```bash
/usr/local/ssl/bin/openssl list -providers -provider oqsprovider -verbose
```

### Generate post-quantum certificates

You next need to generate post-quantum certificates based on the Module-Lattice-Based Digital Signature Algorithm (ML-DSA).

The ML-DSA is lattice-based signing that protects against quantum computing threats. It is formerly known as Dilithium.

```bash
# CA key and certificate
/usr/local/ssl/bin/openssl req -x509 -new -newkey dilithium3 -keyout ca.key -out ca.crt -nodes -subj "/CN=Post-Quantum CA" -days 365

# Server key and CSR
/usr/local/ssl/bin/openssl req -new -newkey dilithium3 -keyout server.key -out server.csr -nodes -subj "/CN=your.domain.com"

# Sign the server certificate
/usr/local/ssl/bin/openssl x509 -req -in server.csr -out server.crt -CA ca.crt -CAkey ca.key -CAcreateserial -days 365
```

{{< call-out "note" >}}
Algorithm identifiers can vary by OQS version (for example, **Dilithium** vs **ML-DSA** naming). To list what your build supports, run the following command:

 ```bash
 /usr/local/ssl/bin/openssl list -signature-algorithms -providers
 ```
{{< /call-out >}}

### Ensure NGINX can load the provider configuration

Add this line to the **main** context of `/etc/nginx/nginx.conf`. The NGINX master process inherits this provider.

```nginx
env OPENSSL_CONF=/usr/local/ssl/openssl.cnf;
```

Reload NGINX after adding your server block.

### Configure NGINX Plus for PQC TLS

To set up PQC TLs, create or edit a server block in a file such as `/etc/nginx/conf.d/pqc.conf:


```nginx
server {
    listen              443 ssl;
    server_name         your.example.com;

    ssl_certificate     /path/to/server.crt;
    ssl_certificate_key /path/to/server.key;

    ssl_protocols       TLSv1.3;

    # PQC key exchange group (depends on oqs-provider build)
    # Examples: kyber768, p256_kyber768, x25519_kyber768
    ssl_ecdh_curve      kyber768;

    location / {
        # Helps confirm negotiated group in testing
        return 200 "$ssl_curve $ssl_curves";
    }
}
```

Once you've written this file, reload NGINX:

```bash
sudo nginx -s reload
```

## Test the handshake

From a client with OpenSSL 3.x, run the following commands::

```bash
openssl s_client -connect your.example.com:443 -tls1_3 -groups kyber768
```

Alternatively, run the following `curl` command on the endpoint and check the response:

```bash
curl -sk https://your.example.com/
```

You should see the values returned by `$ssl_curve $ssl_curves`.

## Troubleshooting

- **Provider does not load**
  - Keep `env OPENSSL_CONF=/usr/local/ssl/openssl.cnf;` in `nginx.conf` and reload.
  - Confirm provider modules exist under `/usr/local/ssl/lib/ossl-modules/`.

- **Unknown algorithm or group**
  - List available groups and adjust `ssl_ecdh_curve`:
    ```bash
    /usr/local/ssl/bin/openssl list -groups -providers
    ```

- **Handshake fails with clients**
  - PQC support is not yet universal. Test with OpenSSL 3.x on the client and align the group list with what both sides support (for example, `p256_kyber768`).

- **Certificate errors**
  - Ensure the Dilithium (or ML-DSA) chain is correct and trusted, or use `-CAfile ca.crt` with `openssl s_client`.

## If you need to revert

To revert to TLS without PQS, take the following steps:

- Open any NGINX configuration files that you've changed.
- Find any `server` blocks that you've changed.
  - Remove the `env OPENSSL_CONF` line
  - Reomve the PQC curve

- Reload NGINX

 You can keep the OQS components installed for later testing.

## Next steps

- Track PQC-related changes in algorithm identifiers and hybrid group names in the OQS project.
- Evaluate **hybrid** groups for gradual migration.
- Monitor NGINX Plus release notes for updates to PQC support.
