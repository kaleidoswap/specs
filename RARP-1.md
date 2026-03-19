# RARP-1: RGB Asset Registry Protocol

**Status:** Draft  
**Version:** 0.1
**Date:** March 2026  
**Authors:** KaleidoSwap Contributors  
**License:** MIT

---

## Problem

RGB has no asset registry. Client-side validation means no global state — which is great for privacy, terrible for discoverability. Today every wallet and exchange maintains its own private list of known assets. Users can't find assets, tickers collide, and the ecosystem is fragmented.

## Proposal

Each RGB service provider hosts a `tokens.json` file at a well-known URL. Clients fetch lists from multiple providers, merge them by `contract_id`, and resolve tickers by consensus.

```
https://kaleidoswap.com/.well-known/rgb/tokens.json
https://utexo.com/.well-known/rgb/tokens.json
https://bitcointribe.app/.well-known/rgb/tokens.json
https://lnfi.network/.well-known/rgb/tokens.json
https://iriswallet.com/.well-known/rgb/tokens.json
```

No new infrastructure. No blockchain changes. Just HTTPS + JSON.

## tokens.json

```jsonc
{
  "name": "KaleidoSwap RGB Token List",
  "version": { "major": 1, "minor": 0, "patch": 0 },
  "timestamp": "2026-03-18T00:00:00Z",
  "network": "bitcoin",
  "provider": {
    "name": "KaleidoSwap",
    "url": "https://kaleidoswap.com"
  },
  "tokens": [
    {
      "contract_id": "rgb:abc123...",
      "iface": "RGB20",
      "ticker": "USDT",
      "name": "Tether USD",
      "precision": 8,
      // optional fields below
      "issued_supply": "100000000000000",
      "description": "USD-backed stablecoin on RGB",
      "icon": "https://kaleidoswap.com/icons/usdt.png",
      "issuer": {
        "name": "Tether Ltd",
        "domain": "tether.to"
      },
      "status": "active",
      "listed_at": "2026-01-15T00:00:00Z"
    }
  ]
}
```

### Mandatory fields per token

| Field | Type | Description |
|-------|------|-------------|
| `contract_id` | string | RGB contract ID (hex). **The only canonical identifier.** |
| `ticker` | string | Uppercase, max 8 chars |
| `name` | string | Human-readable, max 64 chars |
| `precision` | int | Decimal places (0–18) |
| `iface` | string | `RGB20`, `RGB21`, `RGB25` |

### Optional fields

`schema_id`, `issued_supply`, `max_supply`, `description`, `icon`, `issuer` (object: name, domain, pubkey), `status` (active/deprecated/delisted), `listed_at`, `tags`.

## Client Resolution

1. Fetch `tokens.json` from each configured provider
2. Index all tokens by `contract_id`
3. When resolving a ticker:
   - One match across providers → use it
   - Multiple `contract_id`s claim same ticker → show all, display provider count
   - No match → show raw `contract_id`
4. Trust = how many providers list the same `contract_id` with consistent metadata

| Providers | Trust |
|-----------|-------|
| 1 | ⚠️ Low — "Listed by 1 provider" |
| 2 | 🟡 Medium |
| 3+ | ✅ High — "Widely listed" |
| Conflict | 🔴 Flag inconsistent metadata |

## Provider Requirements

- Serve at `https://<domain>/.well-known/rgb/tokens.json`
- `Content-Type: application/json`
- `Access-Control-Allow-Origin: *`
- Increment `version` on every change (semver: patch=fixes, minor=add/remove tokens, major=schema changes)
- Set `status: "deprecated"` instead of removing tokens

## Optional: Issuer Domain Verification

Inspired by Liquid's Asset Registry. Issuers prove ownership by hosting:

```
https://<issuer-domain>/.well-known/rgb/issuer.json
```

```json
{
  "issuer": "Tether Ltd",
  "contracts": [
    { "contract_id": "rgb:abc123...", "ticker": "USDT", "network": "bitcoin" }
  ],
  "timestamp": "2026-03-18T00:00:00Z"
}
```

Clients and providers can verify the domain serves this file → strong real-world identity signal.

## Comparison

| | RARP-1 | Liquid Registry | Uniswap Lists | Status Quo |
|---|---|---|---|---|
| Permissionless | ✅ | ❌ Blockstream gatekeeper | ✅ | N/A |
| Decentralized | Multi-provider | Single server | Multi-list | Fragmented |
| Infrastructure | HTTPS only | HTTPS + domain proof | HTTPS + IPFS | Per-app DB |
| Effort to adopt | ~1 hour | Medium | Low | N/A |

## TODO

- [ ] **Formal JSON Schema** — publish a `tokens.schema.json` for validation
- [ ] **Signature support** — should providers sign `tokens.json`? PGP? Nostr? Both? Need to decide if this is worth the complexity
- [ ] **Icon spec** — max dimensions, format (PNG only? SVG?), hosting requirements
- [ ] **Rate limiting guidance** — recommended client polling interval vs provider expectations
- [ ] **Nostr overlay spec** — event kinds for community-sourced metadata (long-tail assets not listed by any provider). Optional but needs speccing if we go there
- [ ] **NFT / RGB21 / RGB25 metadata** — current schema is fungible-focused. Collectibles need additional fields (media_url, collection_id, etc.)
- [ ] **Testnet coordination** — who hosts the first testnet `tokens.json`? Do we need a shared testnet token list?
- [ ] **Governance** — how do we handle disputes? (e.g., two legit projects want the same ticker). Probably out of scope — let the market decide — but worth discussing
- [ ] **Reference implementation** — Rust + TypeScript client library for fetch/merge/resolve
- [ ] **Versioning strategy for the spec itself** — how do we evolve RARP without breaking existing providers?


## Contributing

Open an issue or PR. This is an ecosystem standard — it only works if multiple providers adopt it.

---

*RARP-1 is an open standard proposed by KaleidoSwap for the RGB ecosystem.*