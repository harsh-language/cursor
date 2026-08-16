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
4. **Shipped visual language only.** Tokens, type, spacing, radii, icons, and
   components come from the source tree. Never invent colors, type scales, or
   chrome. If a visual asset is missing, compose from the nearest sibling
   component in code using existing tokens — so the handoff stays on-brand
   instead of inventing a parallel system.
5. **Unique designs only.** One artboard per visually distinct screen/state.
   Skip pure logic, duplicate layouts, and development-only UI unless the user
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
9. **Complete token specimens.** Before drawing Foundations spacing/radius,
   list every key in the source token object. Paper must show every key.
   Missing Paper tokens → `create_tokens` first. A curated “common”
   subset is a sync failure.
   Why: handoff is the system of record for designers; a short scale
   implies those sizes don’t exist in code.
10. **Foundations numeric labels always include `px`.**
    Format: `{token} {n} px` (e.g. `space-md 16 px`, `radius-sm 8 px`).
    Bare numbers after the token name are a failure.
    Why: without the unit, readers can’t tell scale from unitless ratios.
11. **Icons = source SVG only.** Never freehand paths. Always copy
    `viewBox`, path `d`, stroke/fill, and line caps/joins from the
    project’s icon wrappers or icon-package source files.
    Why: near-miss glyphs look “fine” in screenshots but break visual
    parity with the shipped app.

## Decision flow

```
Paper Desktop open + target file known?
├── No → ask once for file URL / open Paper; wait
└── Yes → resolve SOURCE
    ├── User named commit/stage → that pin
    └── Else → working tree (live)
Discover inventory (tokens → components → unique screens/states)
└── Record flow parity specifications
    ├── Any specification insufficient → continue discovery; no Paper mutation
    └── All specifications sufficient
        └── Record flow-page composition
            ├── Composition incomplete → no flow-artboard mutation
            └── Composition complete
                ├── Paper file empty / no matching system → FULL BUILD
                └── Paper has Foundations/Components/flows → INCREMENTAL SYNC
                    (walk "What changed?" tree below)
Always: screenshot + checkpoints per section → finish_working_on_nodes
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

Never delete-and-rebuild whole pages on incremental unless the full-build tree
above selected FULL BUILD.

## Workflow checklist

Copy and track:

```
Paper sync:
- [ ] 0. Paper MCP ready + file open + comments triaged
- [ ] 1. Source locked (live | pin)
- [ ] 2. Inventory built
- [ ] 2a. Flow parity specifications sufficient
- [ ] 2b. Flow-page composition specified
- [ ] 3. Tokens in Paper
- [ ] 4. Foundations boards
- [ ] 5. Components boards
- [ ] 6. Flow pages (unique states)
- [ ] 7. Section screenshots + source-parity gates
- [ ] 8. Coverage report (done / skipped / blocked)
```

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

#### Repeated-content completeness

For every list, catalogue, menu, tab set, or grouped collection:

```
Trace runtime content path
source → selector → filter → grouping → order
├── Enumerate every production-visible item from live source
├── Record expected item count, group count, names, order + selection states
└── Never use a screenshot subset or demo sample as the complete inventory

Paper scroll content vs recorded inventory
├── Names, counts, order, grouping + selections match? → pass
└── Any mismatch → fail; add/fix content before leaving the page
```

Viewport clipping is allowed; missing items inside the scroll content are
never allowed. Why: off-screen content still belongs to the shipped flow.

#### Source-derived geometry

Before drawing any control or positioned chrome, inspect and record:

- component style/layout definitions
- parent shell/layout styles
- helper functions
- safe-area calculations
- absolute-position formulas
- min/max/step/value mappings
- hidden elements that still consume layout space

```
Source geometry or positioning math exists?
├── Yes → always use it; never derive percentages or positions by eye/screenshot
└── No → derive from measured source layout constraints and record the formula
```

For every slider, calculate the value index and knob travel with the app's
own mapping: source bounds + step + value + knob width + track padding.
Verify Paper track, fill, and knob positions against that calculation.
Why: a visually plausible control can still represent the wrong value.

#### Scrolling and overlay layering

For every fixed viewport containing scrollable content:

```
Source uses a scroll-fade component or manual gradient overlays?
├── Yes → record top/bottom fade height, offsets, initial visibility + z-order
│   Fade visible in represented state?
│   ├── Yes → reproduce it as a visible design layer
│   └── No → omit it for that state
└── No → never invent a fade

Pinned title/footer/accessory + fades
├── Layer order matches source? → pass
└── Else → fix z-order before screenshot
```

Always derive footer and accessory positions from shell styles and safe-area
helpers; never estimate from the screenshot. Why: pinned chrome defines the
usable viewport and the scroll-content boundary.

#### Flow-screen state matrix

For each screen, trace source conditions that materially change:

- content
- control enabled/disabled state
- selection state
- expanded/collapsed regions
- overlays
- empty/populated composition
- reachable loading/error state

```
Source condition is reachable?
├── No → never create the state
└── Yes → does it produce a visually distinct composition?
    ├── Yes → add one artboard and label the state
    └── No → deduplicate under Unique vs skip

Any visually distinct reachable state absent from the matrix?
└── Yes → specification fails; continue discovery
```

Why: route inventory alone misses meaningful states, while unchecked
combinations create duplicate or impossible artboards.

### 2b — Flow-page composition (mutation gate)

Every flow page is a documented handoff surface:

```
Flow-page composition
├── One Overview board at page top
├── One band label above each distinct row/composition
├── One caption frame directly below every screen artboard
└── Spacing tokens/values for overview, bands, rows, captions + gutters
    Any missing → never draw flow artboards
    All present → composition passes
```

Never place orphan screen artboards on a blank page. Why: pixel-accurate
screens without flow structure force the reader to reconstruct sequence,
state ownership, and source mapping.

#### Overview and context chrome

Overview board chrome always matches Foundations/Components doc boards:
**padding 48px**, **gap 48px**, `height: fit-content`, app ground token.

Overview order:

1. Page eyebrow
2. Source page-title style → flow name
3. One-line purpose
4. Facts row when source defines entry, required inputs, exit, or flow shell;
   include the flow-shell source path

Context typography:

| Role | Required source style |
| --- | --- |
| Overview, band, and caption title | Product's highest page/document-title style |
| Eyebrow and source path | Label / secondary style |
| Note | Secondary body style |

```
Source defines a page/document-title style?
├── Yes → copy its exact family, size, weight, leading, tracking + case
└── No → use the highest-level title style shipped in product screens;
         record its exact metrics before drawing
```

The main title in every context frame always uses the source page-title style;
never label-sized type. Why: label-sized titles collapse page structure into
metadata noise.

#### Screen captions

Every caption frame contains, in order:

1. **Eyebrow** — step index or state class; state eyebrows include parent-step
   identity.
2. **Title** — short screen/state name in the source page-title style.
3. **Note** — purpose, progression gate, and relevant control
   defaults/bounds/steps.
4. **Source** — exact source file path for the screen or dominant component.

```
Caption fact exists in source?
├── Exact action label → copy it verbatim; never replace it with “next”/“continue”
├── Required/optional field → state it
├── Default/bound/step → state the source value
└── Reachable-state condition → state the exact condition
```

Never invent a missing caption fact. Why: captions are the handoff map between
the rendered screen, its behavior, and its owner in code.

#### Multi-band spacing and state packing

Before drawing, choose named values from the product spacing scale:

```
Overview → first band = large
Band label → screen row = medium
Screen → caption = small
Between screen artboards = one consistent file-wide gutter
End of band captions → next band label = inter-band

inter-band > every within-band gap?
├── No → choose a larger source spacing value
└── Yes → pass
```

Never eyeball these gaps. Distinct bands must read as separate compositions,
not one dense row.

State/variant rows:

```
Collect only visually distinct reachable variants
└── Pack left-to-right in reading order
    ├── Empty columns for steps without variants? → remove them
    └── Parent-step identity absent? → add it to each caption eyebrow
```

Never force state artboards under parent-step columns. Why: column holes break
reading order without adding state meaning.

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

Spacing / radius board — before screenshot:

```
Source token keys vs Paper specimens
├── Any source key missing from Paper tokens?
│   └── Always create the token, then draw the specimen
├── Any source key missing a specimen row/shape?
│   └── Always add it — never ship a subset
└── Every specimen label
    ├── Ends with ` px`? → pass
    └── Else → rewrite label; never leave bare numbers
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
screenshot + checkpoints.

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

Before declaring a flow page complete:

```
Flow completion gate
├── Flow-page composition gate (§2b) passes?
├── Active-page capture gate passes?
├── Screenshot checkpoints pass?
└── Flow parity specification comparisons pass?
    Any No → page fails; fix and repeat the gate
    All Yes → page complete
```

A polished screenshot never overrides a failed source comparison. Any missing
expected item or reachable state fails the page.

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
| Spacing / radius completeness + units | Compare source token key count and names to Paper rows/shapes; add every missing token/specimen and rewrite every numeric label to `{token} {n} px` |
| Component variety × state matrix | Compare every source variety and its implemented states to labeled Paper specimens; add every missing shipped state and remove invented states |
| Icon path fidelity | Compare `viewBox`, path `d`, stroke/fill, and caps/joins to source wrappers/package files; replace every freehand or near-match glyph |
| Dense-control geometry + states | Compare padding, gaps, fixed dimensions, radii, borders, and interactive states to source layout values; rebuild approximations and add missing states |
| Repeated-content parity | Compare Paper names, counts, order, groups, and selection states to the recorded runtime inventory; add every missing off-screen item |
| Flow-state coverage | Compare artboards to the recorded reachable state matrix; add missing distinct states and remove impossible or duplicate states |
| Scroll / fade / pinned chrome | Compare fade visibility, dimensions, offsets, footer/accessory positions, and z-order to source shell/helpers; fix every mismatch |
| Flow-page composition | Run §2b in full; any missing overview, band, caption, title style, spacing relation, or packing rule fails the page |
| Active-page capture | Confirm target page active; treat height 0, blank screenshot, or empty export as hard failure and recover before verification |
| Repeated rows (lists, nav) | Fixed-width icon and trailing-action slots; columns align across rows |
| Contrast / hierarchy | Compare theme, text roles, and primary-action style directly to source; fix every mismatch |
| Decoration / shadows / cards | Only if the source product uses them — remove invented chrome |
| Placeholder copy | Use source demo/seed/runtime fixture content; if unavailable, use the shortest source-valid example; remove “Lorem” and design-tool names |
| Handoff completeness | Page identifies flow purpose, entry, exit, required inputs, primary-path order, primary vs state artboards, each screen's purpose, and its owning source file; add missing context even when screens are pixel-accurate |

## References

- [coverage-template.md](references/coverage-template.md) — inventory +
  report skeleton
