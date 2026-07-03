# fx-catalog

The **Catalog** for Nathan Colby's Premiere Pro plugin constellation
(EMUL8, LutOven, Cart itself, and future products).

`catalog.json` lists every product's current version and where its signed,
notarized installer lives. It is the single source of truth that the **Cart**
app on users' Macs reads to know whether updates exist. Every release commit
here was made automatically by **Releaser**'s Catalog step — this repo's git
history is the audit log of every version ever announced.

## For agents: how this repo fits

```
Releaser (release pipeline, dev Mac) ──writes──►  catalog.json  ◄──reads── Cart (user Macs)
```

- **Never edit `catalog.json` by hand.** Releaser rewrites it via the GitHub
  contents API on every product release. Manual edits will be clobbered and
  can desynchronize users. Recovery/backfill is
  `releaser catalog --manifest <product-repo>/.releaseflow.json`.
- **Adding a product** happens in the *product's* repo, not here — follow
  "Onboarding a NEW product" in the Releaser repo's `docs/PREMIERE-PLUGINS.md`
  (`~/Documents/Claude Code/Releaser`). The product appears here on its first
  release.
- This repo must stay **public**: Cart and end users fetch
  `https://raw.githubusercontent.com/NathanColby/fx-catalog/main/catalog.json`
  anonymously.

## The format

```json
{
  "catalog_version": 1,
  "updated": "2026-07-02T23:49:41Z",
  "products": [ { …one entry per product… } ]
}
```

Per product:

| Field | Meaning | Rules |
|---|---|---|
| `id` | Permanent product id (`emul8`) | Never changes after first ship. Cart's key. |
| `name` | Display name shown in Cart | Free to change. |
| `kind` | `pkg` (Cart installs it) or `ccx` (Adobe UXP package; Cart links out) | |
| `version` / `build` | The released product version | From the product's `release_config.sh`. |
| `url` | **Version-pinned** installer URL (`releases/download/v…/<Asset>.pkg`) | Never `latest/` — the entry and the file can't drift apart. |
| `notes_url` | The GitHub release page | |
| `receipt` | The pkg identifier (`com.colbymakes.emul8`) | Cart asks `pkgutil` about it (system volume, then `--volume $HOME`) to learn the installed version. Permanent. |
| `min_host` | Human-readable requirement line | Optional. |
| `published` | Release date (UTC) | |

Consumers must **ignore unknown fields** — the format may grow (e.g. an
Ed25519 signature per entry) without breaking older Carts. Trust does not
depend on this file alone: Cart independently verifies every downloaded
installer (Gatekeeper notarization assessment + signer pinned to Team ID
`C5QWK4S58T`) before opening it.
