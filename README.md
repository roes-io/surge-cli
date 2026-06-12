# Surge CLI

`surge` is the command-line tool for **deploying and operating** your self-hosted
Surge instance, and the **customer-kit** is the deployment bundle (Docker Compose
+ nginx + a TLS certificate helper).

Download both from this repository's **[Releases](../../releases)**, verify them,
and follow the steps below.

> The **Outpost** field client and the **sp00fer Relay** are downloaded from
> inside your running portal under **Settings → Downloads** — they are not
> published here.

---

## What's in a release

| Asset | Description |
|---|---|
| `surge-linux-amd64`, `surge-linux-arm64` | the `surge` CLI for 64-bit Linux (Intel/AMD and ARM) |
| `customer-kit-<version>.tar.gz` | Docker Compose files, nginx config, `generate-cert.sh`, and `env.example` |
| `SHA256SUMS` | checksums for every file in the release |
| `SHA256SUMS.minisig` | signature you use to confirm the download is authentic |

Always use the release whose version matches the Surge image you are running.

---

## 1. Download and verify

Verifying confirms the files are authentic and untampered before you run them.

```bash
VER=v0.1.3        # the release you want

# Download (you can also use the "Assets" links on the Releases page)
gh release download "$VER" --repo roes-io/surge-cli -D surge-$VER
cd surge-$VER

# a) Confirm authenticity — the signature must verify against the Surge key
minisign -Vm SHA256SUMS -P RWTbeunE9qFj5+gdlSuoVUvzo/R1ec9RLuGpsx2aioSFDkMgeYLlNuLw

# b) Confirm integrity — every file matches its checksum
sha256sum -c SHA256SUMS
```

Both commands must report success. The verification key for every Surge release is:

```
RWTbeunE9qFj5+gdlSuoVUvzo/R1ec9RLuGpsx2aioSFDkMgeYLlNuLw
```

> Install `minisign` first if you don't have it: `sudo apt install minisign`
> (enable the *universe* repo if the package isn't found), or grab the static
> binary from the [minisign releases](https://github.com/jedisct1/minisign/releases).

---

## 2. Install the CLI

```bash
sudo install surge-linux-amd64 /usr/local/bin/surge   # use -arm64 on ARM hosts
surge version
```

---

## 3. Deploy

Unpack the kit and run the guided deployment:

```bash
tar xzf customer-kit-<version>.tar.gz
cd customer-kit
surge deploy
```

`surge deploy` walks you through hostnames, TLS, your activation key, and registry
access (your vendor provides the activation key and access token). When it
finishes it prints a one-time admin password — change it on first login.

---

## 4. Update

From your deployment directory:

```bash
sudo surge update --to <version> --force --auth-token-env GITHUB_TOKEN
```

`surge update` pulls the new release, snapshots your database, swaps the stack,
runs a health check, and **automatically rolls back** if anything fails. Your data
is preserved across updates.

---

## Requirements

- A 64-bit Linux host (amd64 or arm64) with Docker Engine + the Compose plugin.
- The activation key and registry access token supplied by your vendor.

A full operator guide ships inside the kit at `customer-kit/README.md`.
