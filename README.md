# KaleidoSwap Specifications

This repository contains the specifications published by [KaleidoSwap](https://kaleidoswap.com):

- **OpenAPI specs** for the KaleidoSwap Maker API, versioned per release
- **Protocol specs** proposed to the wider RGB ecosystem

The rendered API reference, with an interactive playground, lives on the docs site: [docs.kaleidoswap.com/api-reference/introduction](https://docs.kaleidoswap.com/api-reference/introduction).

## Structure

```
specs/
├── kaleidoswap.json          # Latest KaleidoSwap API spec
├── versions/                 # One copy per kaleidoswap-maker release
│   ├── kaleidoswap-v0.4.0-rc2.json
│   ├── kaleidoswap-v0.4.0.json
│   ├── ...
│   └── kaleidoswap-v0.5.0.json
├── RARP-1.md                 # RGB Asset Registry Protocol (draft)
├── LICENSE
└── README.md                 # This file
```

## OpenAPI specs

**Latest version:**
```
https://raw.githubusercontent.com/kaleidoswap/specs/main/kaleidoswap.json
```

**Specific version:**
```
https://raw.githubusercontent.com/kaleidoswap/specs/main/versions/kaleidoswap-v{version}.json
```

The `{version}` in the file name is the kaleidoswap-maker release that produced the spec (for example `v0.5.0`). It is not the `info.version` field inside the spec.

### Automatic updates

The OpenAPI specs are pushed by KaleidoSwap CI on each kaleidoswap-maker release. There are no workflows in this repository. Each update:

1. Replaces `kaleidoswap.json` with the spec from the new release
2. Adds a versioned copy in `versions/`

### Manual usage

```bash
# Download latest spec
curl -o kaleidoswap.json \
  https://raw.githubusercontent.com/kaleidoswap/specs/main/kaleidoswap.json

# Download specific version
curl -o kaleidoswap.json \
  https://raw.githubusercontent.com/kaleidoswap/specs/main/versions/kaleidoswap-v0.5.0.json
```

## Protocol specs

### RARP-1: RGB Asset Registry Protocol

**Status:** Draft

RGB has no global asset registry, so every wallet and exchange keeps its own list of known assets. RARP-1 proposes that each RGB service provider host a `tokens.json` file at `https://<domain>/.well-known/rgb/tokens.json`. Clients fetch lists from several providers, merge them by `contract_id` (the only canonical identifier) and resolve tickers by how many providers agree. Issuers can optionally prove domain ownership with an `issuer.json` file. No new infrastructure is needed: just HTTPS and JSON.

Read the draft: [RARP-1.md](RARP-1.md). Feedback is welcome through issues and PRs.

## License

MIT
