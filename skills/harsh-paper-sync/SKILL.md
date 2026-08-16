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
flows. Code stays in the repo; Paper is the handoff surface. Invent nothing
outside the source tree.

## Hard rules

1. **Start immediately.** On invoke, run the workflow. Ask only when a hard
   blocker is true (no Paper file, MCP down, source unclear after one pass).
2. **Source = live designs.** Default source is the **working tree**
   (uncommitted included). Pin to a commit/stage only if the user names one
   (`HEAD`, stage name, SHA). Never silently drop live work; the working tree
   is the only source that includes the current UI.
3. **Paper only.** Do not edit app code, docs, git, or other skills during a
   sync unless the user explicitly asks.
4. **Source visual language only.** Tokens, type, spacing, radii, icons,
   components, copy, and chrome come from source. Never invent a parallel
   system; when an asset is missing, compose from the nearest source sibling.
5. **Unique designs only.** Apply [Unique vs skip](#unique-vs-skip) and the
   [flow-state gate](#2a--flow-parity-specification-mutation-gate). Never add
   duplicate, impossible, or development-only artboards unless requested.
6. **Clone over rewrite.** Build specimens once on Components boards; use
   `<x-paper-clone>` on flow screens. Prefer `duplicate_nodes` +
   `set_text_content` / `update_styles` when syncing an existing file.
   Why: one specimen source keeps flow screens consistent and makes
   incremental syncs patchable instead of divergent copies.
7. **Verify every section.** After each Foundations board, Components board,
   and flow page: screenshot → run [Verification](#verification) → fix → repeat.
   Why: visual polish cannot prove token, content, state, or geometry parity.
8. **Stop on MCP limits.** If Paper write/rate limits hit, report exactly what
   landed and what remains. Do not invent a partial “done.”
9. **Tokens before pixels.** Create/update Paper tokens from source, then bind
   every token-backed property to `var(--token)`. Follow [§3](#3--tokens);
   literals that bypass available tokens fail verification.
   Why: a fresh handoff has no Paper library, and literals cannot update with
   the system.

## Workflow

```
Paper sync:
- [ ] 0. Paper ready + comments triaged
- [ ] 1. Source locked (live | pin)
- [ ] 2. Inventory + unique states
- [ ] 2a. Flow parity specifications sufficient
- [ ] 2b. Flow-page composition specified
- [ ] Mode selected (full | incremental)
- [ ] 3. Paper tokens created/updated
- [ ] 4. Foundations complete
- [ ] 5. Components complete
- [ ] 6. Flow pages complete
- [ ] Verification passed per section
- [ ] 7. Coverage report + finish
```

### Unique vs skip

```
Candidate screen/state
├── Development-only / preview-only chrome, and user did not ask for it?
│   └── Yes → SKIP
├── No visual UI (logic, store, test, util only)?
│   └── Yes → SKIP
├── Same layout structure as an artboard already listed
│   (same regions, chrome, primary controls — only copy/data differs)?
│   └── Yes → SKIP (one artboard; note variants that change copy or behavior)
└── Visually distinct layout, chrome, or primary state
    (empty vs filled, sheet open, confirm, error, different IA)?
    └── Yes → INCLUDE as its own artboard
```

Deduplicate by **layout structure**, not by route name.

### Full build vs incremental

```
Paper has identifiable Foundations, Components + flow-page containers?
├── No (empty, unrelated, or root structure cannot be mapped) → FULL BUILD
├── User asked for a full rebuild → FULL BUILD
└── Yes → INCREMENTAL SYNC; patch/add individual source items in place
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

Never delete-and-rebuild whole pages on incremental unless the mode tree
selected FULL BUILD.

### 0 — Paper ready

1. `get_guide({ topic: "paper-mcp-instructions" })` once per session.
2. `get_basic_info` on the target file (URL from user, or sticky file).
3. `get_font_family_info` for the project's UI font before any type styles.
4. `list_comment_threads`; read every open thread relevant to the sync before
   editing.
5. If MCP fails → tell user to open Paper Desktop with the file; stop.

Open comment gate:

```
Relevant open Paper comment?
├── Yes → convert it into a measurable acceptance check before editing
│   Final screenshot + source comparison passes that check?
│   ├── Yes + user authorized resolving comments → resolve the thread
│   ├── Yes + no resolve authorization → leave open; report passed
│   └── No → leave open; report unresolved + failed check
└── No → continue
```

Never resolve a comment because an edit was made; resolve only after its
acceptance check passes. Why: a changed screen is not proof that the reported
parity defect is fixed.

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

Skip: tests, stores, DB, development-only menus, non-visual utilities.

### 2a — Flow parity specification (mutation gate)

Before any Paper mutation, record this specification for every unique screen:

```
Flow parity specification
- Source screen + shell files
- Shared components used
- Repeated-content runtime data source
- Exact rendered copy
- Layout regions + sizing rules
- Safe-area / footer / overlay behavior
- Scrolling + fade behavior
- Control values, bounds, steps + positioning math
- Reachable visual state matrix
- Expected item + group count/order for data-backed content
- Relevant Paper-comment acceptance checks
```

```
Can every field be answered from live source?
├── No → continue discovery; never draw the screen
└── Yes
    Can another agent build and verify the screen without reopening source?
    ├── No → add the missing values, formulas, states, counts, or layering
    └── Yes → specification is sufficient; Paper mutation may begin
```

Why: screenshots reveal appearance, not hidden content, runtime state paths,
or positioning math. The specification makes source parity testable before
drawing starts.

#### Parity gates

| Gate | Record from source | Pass condition |
| --- | --- | --- |
| Repeated content | Runtime source → selector → filter → group → order; every production-visible item; counts, names, order, selections | Paper scroll content matches, including clipped items; never infer completeness from screenshots/demo subsets |
| Geometry | Component + shell layout, helpers, safe-area/absolute formulas, hidden layout consumers, min/max/step/value mappings | Paper uses source math; when no formula exists, record the measured constraint used |
| Sliders | Bounds, step, value, knob width, track padding, value-index + travel logic | Track, fill, and knob match the calculated source position |
| Scroll/overlays | Fade dimensions/offsets/visibility/z-order; pinned title/footer/accessory positions | Visible layers and order match source; never invent a fade |
| States | Reachable conditions changing content, controls, selection, expansion, overlays, empty/populated, loading/error | One artboard per visually distinct reachable state; none missing, impossible, or duplicate |

Never estimate when source data or math exists. Why: screenshots hide
off-screen content, impossible states, and numerically wrong controls that
still look plausible.

### 2b — Flow-page composition (mutation gate)

Specify this before drawing flow artboards:

| Layer | Required structure |
| --- | --- |
| Overview | Top board; doc chrome (`48px` padding/gap, `fit-content`, app ground); eyebrow → source page-title style → purpose → facts (entry, inputs, exit, shell path) when present |
| Bands | One label above each composition; source page-title style |
| Screens | One caption directly below each artboard: step/state eyebrow → source page-title screen name → purpose/gate/control facts → owning source path |
| Facts | Exact source action labels, required/optional status, defaults/bounds/steps, and reachable-state condition; never generic “next/continue” |
| Type | Titles use exact source page/document-title metrics; eyebrows/paths use label/secondary, notes use secondary body |
| Spacing | Record source-scale values for overview→band, band→row, screen→caption, artboard gutter, and inter-band gap; inter-band must exceed every within-band gap |
| Variants | Pack visually distinct reachable states left-to-right; no empty parent columns; parent step lives in the caption eyebrow |

```
Any overview, band, caption, title metric, spacing value, or packing rule absent?
├── Yes → do not draw flow artboards
└── No → composition passes
```

Never place orphan screens on a blank page or invent caption facts. Why:
handoff context must expose sequence, state ownership, behavior, and code
ownership without reconstructing them from the repo.

### 3 — Tokens

This skill owns Paper tokens for the handoff. They are not assumed to exist.

```
Mode
├── FULL BUILD / empty Paper file
│   └── create_tokens for every visual key in the source theme/token files
│       (colors, type, space, radius, border, overlay, shadow, icon size,
│        motion if visual) before Foundations, Components, or flows
└── INCREMENTAL SYNC
    ├── Source key missing from Paper → create_tokens for that key
    ├── Paper token value/name drifted → update it to match source
    └── Unchanged → leave it
```

Never invent token names or values. Always mirror source names and values.
Never draw boards or screens until the token set for the current inventory
pass exists in Paper — why: later writes must bind to `var(--token)`, and a
fresh code→canvas sync has no prior Paper library to lean on.

Token application — walk this for every property on every `write_html`,
`update_styles`, and cloned node:

```
Does the source set this property from a token / theme value?
├── Yes
│   ├── Matching Paper token exists? → write var(--token); never the literal
│   └── No matching Paper token? → create_tokens first, then write var(--token)
└── No (one-off layout math, source-hardcoded value)
    └── Write the literal and record why it is not tokenized
```

Coverage check before leaving any board or screen:

```
Read back the node's styles
├── Any token-backed property still holding a literal
│   (hex/rgb color, px spacing/padding/gap, radius, border width,
│    font size/weight/leading/tracking, icon size, overlay, shadow)?
│   └── Fail; rebind it to var(--token) and re-check
└── All token-backed properties reference var(--token)? → pass
```

Literal values are allowed only for Foundations specimens documenting a raw
value, source-hardcoded properties, and skill-owned doc chrome with no matching
source token. If a matching source token exists, doc chrome binds to it too.
Why: the handoff is only a system of record while Paper values move with the
source tokens; hand-applied numbers freeze the file at one commit.

### 4 — Foundations page

Wide doc boards: Colors (semantic roles), Type
(size/weight/leading/tracking/case), and Spacing + radius.

```
For every visual source-token key
├── Paper token missing? → create it
├── Foundations specimen missing? → add it; never ship a subset
└── Numeric size/radius label
    ├── Format `{token} {n} px`? → pass
    └── Else → rewrite; never leave a bare number
```

Doc board chrome: **padding 48px**, **gap 48px**, `height: fit-content`,
ground = app background token. Header pattern: eyebrow (page) → title → one
line purpose.

### 5 — Components page

One specimen board per component family from inventory. Build each visual
variety once here for cloning.

For each visual variety in the family (not the family name as one blob):

```
Source component implements state S?
├── Yes → always ship a labeled specimen for S
└── No → never invent S

Any variety still missing a shipped state?
└── Always finish the matrix before leaving the board
```

Dense controls (sliders, timers, segmented controls, combo pills):

1. Read source layout numbers (padding, gap, fixed widths, radii, borders).
2. Match source style/layout values exactly. If a design-tool component set
   exists for the control, use it only to confirm variants the source ships.
3. Always include every shipped interactive state for that control.
4. “Looks close” is a failure.

Icons always copy source `viewBox`, path `d`, stroke/fill, and caps/joins;
never freehand. Repeated rows use fixed icon/action slots so columns align.

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
- Use exact demo, seed, or runtime fixture content from source. If none exists,
  use the shortest example that satisfies source validation and formatting;
  never use “Lorem.”
- Sheets/menus as overlays on the parent screen state, not orphan chrome.
- Match rendered copy (including `text-transform`) so the handoff matches
  what users see.

Write small, write often: one visual group per `write_html`. Clone from
Components. Build from the flow parity specification, including all recorded
scroll content even when the viewport clips it. After each flow page section:
screenshot + Verification.

Before any flow screenshot or geometry verification:

```
get_basic_info reports target flow page active?
├── No → focus/switch to it with an available Paper action
│   └── No focus action available → ask the user once; stop verification
└── Yes → capture screenshot / measurements

Height = 0, screenshot blank, or export empty?
├── Yes → hard fail; recover active page and retry
└── No → continue
```

Never continue positioning by guess after an empty capture. Why: measurements
from the wrong or inactive page cannot verify layout.

Declare a flow page complete only after §2a parity, §2b composition, the
active-page gate, and Verification all pass.

### 7 — Done

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

## Verification

After every section screenshot, run only the gates that apply:

| Section | Must pass |
| --- | --- |
| All | Non-empty capture; no clipping (`fit-content` on clip); source theme/type/action hierarchy; no invented decoration/copy |
| All | §3 token read-back: every token-backed property uses `var(--token)` |
| Foundations | §4: every source token key has a specimen; numeric labels end in ` px` |
| Components | §5: every source variety/state, exact geometry, source SVGs, aligned repeated rows |
| Flows | §2a parity table + §2b composition gate + §6 active-page gate |

Any failed row blocks continuation. Screenshot polish never overrides source
parity.

## References

- [coverage-template.md](references/coverage-template.md) — inventory +
  report skeleton
