# fx-catalog

The **Catalog** for Nathan Colby's Premiere Pro plugin constellation
(EMUL8, LutOven, and future products).

`catalog.json` lists every product's current version and where its signed,
notarized installer lives. It is the single source of truth that the **Cart**
app (and any "Check for updates" button inside an effect) reads to know
whether an update exists.

## Rules

- **Do not edit `catalog.json` by hand.** Releaser rewrites it automatically
  as the final step of every product release (`releaser release` → Catalog
  step). Each release is one commit here, so this repo's history is the audit
  log of every version ever announced.
- Entries carry **version-pinned** download URLs (tag + asset), so an entry
  and the installer it points at can never disagree.
- Products are managed independently — a release of one product touches only
  its own entry.

Recovery/backfill: `releaser catalog --manifest <product>/.releaseflow.json`.

Design doc: the Releaser repo → `docs/PREMIERE-PLUGINS.md`.
