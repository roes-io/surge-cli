# surge-cli

Host tooling for **Surge** deployments — the `surge` server/admin CLI and the
**customer-kit** (docker-compose + nginx + cert helper). Published here so an
operator can fetch them **before / independent of** a running portal (initial
deploy, and updating `surge` itself).

> This repo holds **no IP or secrets** — just the host orchestrator and the
> compose/nginx config. The Surge application image is private (GHCR, requires a
> licence + access token); these tools do nothing without it. Authenticity of
> every asset is guaranteed by the **minisign signature**, not by access control.

The field clients **outpost** and **sp00fer-relay** are *not* here — download
them from your running portal under **Settings → Downloads**.

## Releases

Each release attaches:

| Asset | What |
|---|---|
| `surge-linux-amd64`, `surge-linux-arm64` | the host CLI (`deploy`, `update`, `backup`, `logs`, …) |
| `customer-kit-<version>.tar.gz` | docker-compose + nginx + `generate-cert.sh` + `env.example` |
| `SHA256SUMS` | sha256 of every asset above |
| `SHA256SUMS.minisig` | minisign signature over `SHA256SUMS` |

## Download + verify

```bash
VER=v0.1.3
# (browser download works too — these are public release assets)
gh release download "$VER" --repo roes-io/surge-cli -D surge-$VER && cd surge-$VER

# 1. authenticity — verify the signature over the checksums
minisign -Vm SHA256SUMS -P RWTbeunE9qFj5+gdlSuoVUvzo/R1ec9RLuGpsx2aioSFDkMgeYLlNuLw

# 2. integrity — verify each file matches
sha256sum -c SHA256SUMS

# 3. install the CLI
sudo install surge-linux-amd64 /usr/local/bin/surge
surge version
```

The **release public key** (safe to publish; baked into every `surge` binary):

```
RWTbeunE9qFj5+gdlSuoVUvzo/R1ec9RLuGpsx2aioSFDkMgeYLlNuLw
```

## Deploy / update (quick reference)

```bash
# first deploy: unpack the kit, then
tar xzf customer-kit-<version>.tar.gz && cd customer-kit
surge deploy                 # interactive: hostnames, TLS, keygen IDs, GHCR token

# later updates (from the project dir)
sudo surge update --to <version> --force --auth-token-env GITHUB_TOKEN
```

Full operator runbook ships with the kit (`customer-kit/README.md`).
