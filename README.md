# m365-plus-plus-hunting-queries

Curated **Microsoft Defender advanced-hunting (KQL) queries** for the
[M365++ browser extension](https://github.com/MrOlof/m365-plus-plus). The
extension fetches this repo at runtime (raw GitHub, like the
`m365-plus-plus-baselines` registry) and renders a searchable, in-context query
library — find a query, copy it, run it in `security.microsoft.com` advanced
hunting. No site-hopping across kqlsearch.com and a dozen GitHub repos.

This repo is **data, not code** — the extension only displays and copies the
KQL, it never executes it.

## How it's consumed

- `manifest.json` — the catalog the extension reads: per-query metadata
  (title, description, category, MITRE tactics/techniques, tables, platform,
  license, credit, source URL, and the path to the `.kql` body).
- `queries/**/*.kql` — **pure KQL bodies**, fetched on demand when a query is
  expanded. Keep these copy-paste-clean (only a short leading `//` comment);
  everything machine-readable lives in `manifest.json`.

## Manifest entry schema

```jsonc
{
  "id": "unique-slug",
  "title": "Human title",
  "description": "What it hunts and what a hit means.",
  "category": "MDE coverage & health",
  "mitreTactics": ["Defense Evasion"],
  "mitreTechniques": ["T1562.001"],
  "tables": ["DeviceInfo"],
  "platform": "defender",          // defender | sentinel | both
  "license": "M365++-original",    // MIT | BSD-3-Clause | M365++-original
  "credit": "@author",
  "sourceUrl": "https://...",
  "file": "queries/.../slug.kql"
}
```

### `platform` matters

Defender advanced hunting and Microsoft Sentinel are **different KQL dialects**
(`Timestamp` vs `TimeGenerated`, the renamed `IdentityInfo` columns, the
`SecurityAlert` → `AlertInfo`/`AlertEvidence` shift). A `sentinel`-tagged query
will silently return nothing if pasted into Defender. The extension shows a
warning badge for those and hides them by default behind a "Defender-ready
only" toggle. Tag honestly; when unsure, prefer the more restrictive tag.

## Adding queries

1. Drop a pure-KQL file under `queries/<category>/<slug>.kql`.
2. Add its entry to `manifest.json`.
3. Bump `manifest.json` `version`.
4. Push — the extension picks it up live, no extension release needed.

## Licensing

- **M365++ originals** (this repo's own queries) — MIT (see `LICENSE`).
- **Forked community queries** — only permissively-licensed sources may be
  bundled, with attribution. Today that means:
  - `Azure/Azure-Sentinel` (MIT) — the Microsoft-endorsed source that also
    feeds the portal's Community Queries tab.
  - `Bert-JanP/Hunting-Queries-Detection-Rules` (BSD-3-Clause) — requires
    retaining the copyright/conditions/disclaimer and the no-endorsement clause.
  Each forked query carries its own `license` + `credit` + `sourceUrl` in the
  manifest, and the upstream license texts live in `THIRD-PARTY-NOTICES.md`.
- **Unlicensed** community repos are **not** bundled — link out instead.

> The first seed is M365++ originals (MDE coverage/health + threat activity).
> These were authored for this repo and should be validated against a live
> Defender XDR (E5 / MDE P2) tenant before being treated as battle-tested.
