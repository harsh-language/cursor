---
name: harsh-paper-sync
description: >-
  Sync a project's live UI into Paper as a code-free design handoff — tokens,
  foundations, components, and every unique flow/state. Use when the user
  invokes /harsh-paper-sync, asks to put the app on Paper, sync designs to
  Paper, export codebase UI to Paper, or refresh a Paper file from live product
  UI.
disable-model-invocation: true
---

# harsh-paper-sync

Put the **live product UI** on Paper the moment this skill is invoked.
Paper becomes a developer-ready, code-free design file: design system + unique
flows. Code stays in the repo; Paper is the handoff surface.

When a product-defaults reference exists for the workspace (e.g.
[ultraload-defaults.md](references/ultraload-defaults.md)), load it after
source lock and apply those page maps / token paths. Otherwise invent nothing
outside the source tree.

## Hard rules

1. **Start immediately.** On invoke, run the workflow. Ask only when a hard
   blocker is true (no Paper file, MCP down, source unclear after one pass).
2. **Source = live designs.** Default source is the **working tree**
   (uncommitted included). Pin to a commit/stage only if the user names one
   (`HEAD`, stage name, SHA). Never silently drop live work — in-progress UI
   is usually what they want on Paper.
3. **Paper only.** Do not edit app code, docs, git, or other skills during a
   sync unless the user explicitly asks.
4. **Shipped visual language only.** Tokens, type, spacing, radii, icons, and
   components come from the source tree. Never invent colors, type scales, or
   chrome. If a visual asset is missing, compose from the nearest sibling
   component in code using existing tokens — so the handoff stays on-brand
   instead of inventing a parallel system.
5. **Unique designs only.** One artboard per visually distinct screen/state.
   Skip pure logic, duplicate layouts, and `__DEV__`-only UI unless the user
   asks for them. Duplicate artboards inflate the file and hide the real
   product surface area.
6. **Clone over rewrite.** Build specimens once on Components boards; use
   `<x-paper-clone>` on flow screens. Prefer `duplicate_nodes` +
   `set_text_content` / `update_styles` when syncing an existing file.
   Why: one specimen source keeps flow screens consistent and makes
   incremental syncs patchable instead of divergent copies.
7. **Screenshot every section.** After each Foundations board, Components
   board, and flow page: `get_screenshot` → run [Screenshot checkpoints](#screenshot-checkpoints)
   → fix → then continue. Why: Paper clips and hierarchy bugs are invisible
   until you look; fixing later means rebuilding whole pages. On clip:
   `height: fit-content` — never guess a new fixed height.
8. **Stop on MCP limits.** If Paper write/rate limits hit, report exactly what
   landed and what remains. Do not invent a partial “done.”

## Decision flow

```
Paper Desktop open + target file known?
├── No → ask once for file URL / open Paper; wait
└── Yes → resolve SOURCE
    ├── User named commit/stage → that pin
    └── Else → working tree (live)
Discover inventory (tokens → components → unique screens/states)
├── Paper file empty / no matching system → FULL BUILD
└── Paper file already has Foundations/Components/flows → INCREMENTAL SYNC
    (walk "What changed?" tree below)
Always: screenshot + checkpoints per section → finish_working_on_nodes
```

### Unique vs skip

```
Candidate screen/state
├── __DEV__ / simulator-only chrome, and user did not ask for it?
│   └── Yes → SKIP
├── No visual UI (logic, store, test, util only)?
│   └── Yes → SKIP
├── Same layout structure as an artboard already listed
│   (same regions, chrome, primary controls — only copy/data differs)?
│   └── Yes → SKIP (one artboard; note data variants in coverage if useful)
└── Visually distinct layout, chrome, or primary state
    (empty vs filled, sheet open, confirm, error, different IA)?
    └── Yes → INCLUDE as its own artboard
```

Deduplicate by **layout structure**, not by route name.

### Full build vs incremental

```
Paper file has Foundations + Components + flow pages that map to this product?
├── No (empty, unrelated file, or page map cannot be matched) → FULL BUILD
├── User asked for a full rebuild → FULL BUILD
└── Yes → INCREMENTAL SYNC
    Token names/roles mostly renamed or Foundations structure abandoned?
    ├── Yes → treat as irreconcilable → FULL BUILD (say so in coverage)
    └── No → patch in place (tokens → specimens → flows)
```

### What changed? (incremental)

Compare **inventory** to existing Paper page/artboard names and token values:

```
For each inventory item
├── Token value or name drifted vs Paper tokens?
│   └── Update tokens first, then patch any specimen/screen using them
├── Component family missing or specimen states incomplete?
│   └── Add/patch Components boards only
├── Flow artboard missing?
│   └── Add that artboard
├── Flow artboard exists but source UI changed?
│   └── Update in place (set_text_content / update_styles / duplicate_nodes)
└── Unchanged → leave it
```

Never delete-and-rebuild whole pages on incremental unless the full-build tree
above selected FULL BUILD.

## Workflow checklist

Copy and track:

```
Paper sync:
- [ ] 0. Paper MCP ready + file open
- [ ] 1. Source locked (live | pin)
- [ ] 2. Inventory built
- [ ] 3. Tokens in Paper
- [ ] 4. Foundations boards
- [ ] 5. Components boards
- [ ] 6. Flow pages (unique states)
- [ ] 7. Section screenshots + checkpoints
- [ ] 8. Coverage report (done / skipped / blocked)
```

### 0 — Paper ready

1. `get_guide({ topic: "paper-mcp-instructions" })` once per session.
2. `get_basic_info` on the target file (URL from user, or sticky file).
3. `get_font_family_info` for the project's UI font before any type styles.
4. If MCP fails → tell user to open Paper Desktop with the file; stop.

### 1 — Source lock

| User says | Source |
| --- | --- |
| (nothing / “live” / “current” / in-progress stage) | Working tree files |
| `HEAD` / commit SHA / named stage or “through \<stage\>” | That git pin via `git show <ref>:path` |
| Explicit exclude list | Honor excludes |

Announce in one line: `Source: live working tree` or `Source: <ref>`.

```
Pinning a commit while the working tree is dirty?
├── Yes → use an isolated worktree / git show; never checkout away the branch
└── No → read files normally
```

Never checkout away the user's branch without asking.

### 2 — Inventory

From the source, build three lists (keep short; store detail in chat only):

1. **Tokens** — theme/token files (colors, type, space, radius, motion if
   visual).
2. **Components** — shared primitives with visual presence. Group into
   specimen boards by family (buttons, inputs, lists/rows, chrome, etc.).
3. **Flows** — screens + states that pass the Unique-vs-skip tree.

Read blueprint / implementation plan / `AGENTS.md` when present to name areas
and must-have screens. Product copy rules in the repo win (authored case vs
rendered case).

Skip: tests, stores, DB, `__DEV__` menus, non-visual utilities.

### 3 — Tokens

`create_tokens` / update existing Paper tokens to match source names and
values. Use CSS variables everywhere (`var(--token)`). No raw hex on flow or
component screens — why: hex bypasses the token system and the handoff drifts
from code the moment a token changes. Raw values belong only on Foundations
swatches that document the token.

### 4 — Foundations page

Artboards (wide doc boards, not device frames):

| Board | Content |
| --- | --- |
| Colors | Semantic swatches + roles |
| Type | Scale specimens with weight/leading/tracking/case |
| Spacing and radius | Space steps + radius specimens |

Doc board chrome: **padding 48px**, **gap 48px**, `height: fit-content`,
ground = app background token. Header pattern: eyebrow (page) → title → one
line purpose.

### 5 — Components page

One specimen board per component family from inventory. Include interactive
states the product actually ships (default / pressed / disabled / empty /
filled / error — only those that exist). Label every specimen with state.
Build once here for cloning.

### 6 — Flow pages

One Paper **page per product area** (from inventory / IA — not a fixed
product list). Artboard size = the product's primary frame:

| Product surface | Default artboard |
| --- | --- |
| Mobile phone | **390×844** unless source uses another breakpoint |
| Tablet / desktop / web | Match the source layout width the UI is designed for |

Device-chrome rules:

- App ground token; column flex; safe layout matching source screens.
- If the product is mobile and ships a system status bar in-app, put the
  platform status bar on every phone screen **except** full-bleed
  splash/brand screens — why: splash is brand theater; other screens need
  the same chrome the device shows so spacing and hierarchy read correctly.
- Realistic content from demo/seed docs when available — not “Lorem.”
- Sheets/menus as overlays on the parent screen state, not orphan chrome.
- Match rendered copy (including `text-transform`) so the handoff matches
  what users see.

Write small, write often: one visual group per `write_html`. Clone from
Components. After each flow page section: screenshot + checkpoints.

### 7 — Incremental sync

Walk the Full-build-vs-incremental and What-changed trees. Patch in place.
Do **not** delete and rebuild whole pages unless those trees selected FULL
BUILD.

### 8 — Done

1. `finish_working_on_nodes`.
2. Coverage report:

```
Source: …
Paper file: …
Mode: full build | incremental sync
Foundations: …
Components: …
Flows shipped: …
Skipped (why): …
Blocked: …
```

## Screenshot checkpoints

After every section screenshot, pass all that apply before moving on:

| Check | Fail → fix |
| --- | --- |
| Clipping / cut-off content | Set artboard `height: fit-content`; re-screenshot |
| Repeated rows (lists, nav) | Fixed-width icon and trailing-action slots; columns align across rows |
| Contrast / hierarchy | Matches live app theme (including dark); primary action is obvious |
| Decoration / shadows / cards | Only if the source product uses them — remove invented chrome |
| Placeholder copy | No “Lorem”, no “Figma”/“Sketch” mentions; realistic product strings |
| Handoff completeness | A designer can understand the screen without opening the repo (labels, states, key content present) |

## References

- [coverage-template.md](references/coverage-template.md) — inventory +
  report skeleton
- [ultraload-defaults.md](references/ultraload-defaults.md) — UltraLoad
  page map and conventions when that product is the workspace
