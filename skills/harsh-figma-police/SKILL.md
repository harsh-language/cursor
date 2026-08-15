---
name: harsh-figma-police
description: Lock a Figma or Paper UI target, map diffs vs code, fix, and re-diff until parity.
disable-model-invocation: true
---

# harsh-figma-police

The locked Figma or Paper target is the source of truth. Diff → fix → re-diff
until a fresh pass finds zero differences.

## Hard rules

1. **One target.** Work only on the UI item the user named or confirmed. Never
   widen to neighboring screens, pages, or components. Because I decided so.
2. **Ask when unclear.** If the design source/target **or** the code
   file/component is unknown, stop and ask once, then wait. Because I decided so.
3. **Design wins.** Always use measured values from the locked Figma or Paper
   target over existing code when they conflict. Because I decided so.
4. **Loop until clean.** After every fix batch, re-map diffs from scratch. Exit
   only when a full re-map finds **zero** differences. Because I decided so.
5. **No silent “close enough.”** Record every mismatch. Fix every implementable
   mismatch. Non-implementable mismatches become named blockers you ask about —
   never mark them as matching. Because I decided so.
6. **No design evidence → pause.** If no design is provided, MCP fails, tool
   limits are hit (e.g. free-plan caps), auth/connection dies, or inspection
   returns nothing usable — stop. Tell the user what blocked you. Ask once,
   then wait. Never invent layout, type, color, spacing, or “best guess”
   parity. Because I decided so.

## Decision flow

```
Is design source (Figma or Paper) + exact target + code target locked?
├── No → ask once, wait
└── Yes → can you inspect the design via MCP / tools right now?
    ├── No (missing design, MCP error, rate/tool limit, auth, empty result)
    │   → pause, report the blocker, ask once, wait — never invent values
    └── Yes → inspect design + code, then for each mismatch:
    ├── Affects rendered appearance of the locked target?
    │   ├── Yes, implementable in current stack → diff-map row → fix
    │   └── Yes, not implementable → named blocker → ask
    └── Token/name only, no visual difference → ignore (not a diff)
After fixes → fresh re-map (never reuse prior map)
├── Diff rows remain → fix → re-map
├── Blockers remain → report blockers, do not mark clean
└── Zero rows and zero blockers → report clean
```

## Workflow

Copy and track:

```
Figma/Paper Police:
- [ ] 1. Lock target (design source + code)
- [ ] 2. Inspect design evidence
- [ ] 3. Inspect code / rendered UI
- [ ] 4. Map differences
- [ ] 5. Fix mapped diffs (batched)
- [ ] 6. Re-map (fresh pass)
- [ ] 7. Repeat 5–6 until map is empty
- [ ] 8. Report clean | blockers
```

### 1. Lock target

Resolve, in order:

1. Design source: Figma or Paper. If both are available, ask which one wins.
2. Exact design target:
   - Figma: URL / file / node.
   - Paper: file / page / artboard or current selection.
3. The specific UI part (screen, component, state, breakpoint).
4. The matching code surface (route, component file, story, CSS module).

If any item is missing or ambiguous → **ask once**, then wait.
Do not map until both sides are locked.

### 2. Inspect design

#### Figma

Use Figma MCP / console tools. Load prerequisite Figma skills before tools that
require them.

Collect node metadata, layout, text, fills, strokes, effects, radius, padding,
gap, bound variables/tokens, and screenshots of the exact target and relevant
variants/states.

#### Paper

Before any Paper tool:

1. Confirm Paper Desktop is running; if connection fails, ask the user to open it.
2. Load `get_guide({ topic: "paper-mcp-instructions" })` once per session.
3. Call `get_basic_info`, then `get_selection`.
4. Lock the selected artboard/node or ask the user to select the exact target.

Collect structure with `get_tree_summary` / `get_node_info`, exact CSS values
with `get_computed_styles`, design tokens with `get_tokens`, assets with
`get_fill_image`, and screenshots with `get_screenshot`. Never infer exact
sizes or colors from a Paper screenshot when computed styles are available.

For either source, **read/inspect first**; mutate code only — never the design
file — unless the user explicitly asked to change the design.

Capture concrete values (px, rem-equivalent, hex/rgba, font size/weight/
line-height/letter-spacing, alignment). Never summarize as “looks tighter.”

### 3. Inspect code

Find the implemented counterpart for the locked target. Use:

- Component source + styles (CSS/Tailwind/tokens)
- Rendered UI when a browser/harness/screenshot path exists
- Design-token files already used by the project

Match the same state/variant/breakpoint the locked design target shows. For
Paper, states must be visible in the locked artboard/selection or separate
artboards; never invent an unseen state.

### 4. Map differences

Produce an explicit diff list before editing. Compare at least:

| Category | Check |
| --- | --- |
| Layout | size, padding, gap, alignment, direction, wrapping |
| Type | family, size, weight, line-height, letter-spacing, color |
| Color | fills, strokes, icons, overlays, opacity |
| Shape | radius, border width, shadows/effects |
| Content | labels, order, icons, empty/loading/error if in frame |
| Interaction | hover/pressed/disabled/focus if shown in the design |
| Density | spacing rhythm vs neighbors **inside** the target only |

Visual parity is required. Token or name mismatches count as diffs only when
they change rendered appearance or block correct implementation of a visual
value.

Format:

```markdown
## Diff map — pass N

| # | Property | Design | Code | Fix |
| --- | --- | --- | --- | --- |
| 1 | … | … | … | … |
```

If the map is empty and blockers are empty → **Report clean**.
If the map is non-empty → fix all implementable rows before the next map.

### 5. Fix

Apply all **independent** mapped fixes for the locked target in one batch.
If a fix depends on measuring a prior fix, split into ordered batches and
re-map after each batch.

- Always use existing project tokens / primitives when they match the design
  value; hard-code only when no matching token exists.
- Never refactor unrelated code.
- Never “improve” beyond the locked design for this run.

### 6. Re-map (mandatory)

First-pass reads miss spacing, type, color, radius, alignment, states, and
density. After each fix batch, prove parity with a fresh pass — never memory
of the prior map:

1. Re-inspect the **same** locked Figma node or Paper artboard/selection
   (fresh screenshot + metadata/computed styles).
2. Re-inspect code / rendered UI.
3. Write a **new** diff map (pass N+1). Never reuse the previous map as proof.

If differences remain → fix again → re-map again.
Stop looping only when pass N reports **zero** rows.

### 7. Report clean

When the map is empty and no blockers affect appearance:

```markdown
## Figma/Paper Police — clean

- Source: Figma | Paper
- Target (design): …
- Target (code): …
- Passes run: N
- Remaining known blockers: none | [list]
```

If blockers remain (missing asset, unsupported CSS, unknown token), list them
and ask how to resolve. Never mark clean while blockers affect the locked
target’s appearance.

## Anti-patterns

- One-shot “I matched the design” without a second empty map
- Fixing only the issues the user already pointed out
- Expanding scope to “while I’m here” polish
- Trusting the first design skim over measured values / screenshots
- Declaring parity from code reading alone with no visual check when tools
  allow one
- Guessing design values when MCP is down, rate-limited, or no design was
  provided — inventing “close enough” instead of pausing and asking
