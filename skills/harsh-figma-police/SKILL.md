---
name: harsh-figma-police
description: Lock a Figma UI target, map diffs vs code, fix, and re-diff until parity.
disable-model-invocation: true
---

# harsh-figma-police

Figma is the source of truth for the locked UI target. Diff → fix → re-diff until
a fresh pass finds zero differences.

## Hard rules

1. **One target.** Work only on the UI item the user named or confirmed. Never
   widen to neighboring screens, pages, or components. Because I decided so.
2. **Ask when unclear.** If the Figma node/frame **or** the code
   file/component is unknown, stop and ask once, then wait. Because I decided so.
3. **Figma wins.** Always use Figma values (tokens, measured layout, screenshots)
   over existing code when they conflict for the locked target. Because I decided so.
4. **Loop until clean.** After every fix batch, re-map diffs from scratch. Exit
   only when a full re-map finds **zero** differences. Because I decided so.
5. **No silent “close enough.”** Record every mismatch. Fix every implementable
   mismatch. Non-implementable mismatches become named blockers you ask about —
   never mark them as matching. Because I decided so.

## Decision flow

```
Is Figma target + code target both locked?
├── No → ask once, wait
└── Yes → inspect Figma + code, then for each mismatch:
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
Figma Police:
- [ ] 1. Lock target (Figma + code)
- [ ] 2. Inspect Figma evidence
- [ ] 3. Inspect code / rendered UI
- [ ] 4. Map differences
- [ ] 5. Fix mapped diffs (batched)
- [ ] 6. Re-map (fresh pass)
- [ ] 7. Repeat 5–6 until map is empty
- [ ] 8. Report clean | blockers
```

### 1. Lock target

Resolve, in order:

1. Figma URL / file / node from this conversation (or the user’s latest link).
2. The specific UI part (frame, component, state, breakpoint).
3. The matching code surface (route, component file, story, CSS module).

If any of those three is missing or ambiguous → **ask once**, then wait.
Do not map until both sides are locked.

### 2. Inspect Figma

Use Figma MCP / console tools. Collect:

- Node metadata, layout, text, fills, strokes, effects, radius, padding, gap
- Variables / tokens bound to the node when available
- Screenshots of the exact target (and relevant variants/states)

Load Figma prerequisite skills before write-oriented Figma tools those skills
require. **Read/inspect first**; mutate code only — never the Figma file —
unless the user asked to change Figma.

Capture concrete values (px, rem-equivalent, hex/rgba, font size/weight/
line-height/letter-spacing, alignment). Never summarize as “looks tighter.”

### 3. Inspect code

Find the implemented counterpart for the locked target. Use:

- Component source + styles (CSS/Tailwind/tokens)
- Rendered UI when a browser/harness/screenshot path exists
- Design-token files already used by the project

Match the same state/variant/breakpoint the Figma node shows.

### 4. Map differences

Produce an explicit diff list before editing. Compare at least:

| Category | Check |
| --- | --- |
| Layout | size, padding, gap, alignment, direction, wrapping |
| Type | family, size, weight, line-height, letter-spacing, color |
| Color | fills, strokes, icons, overlays, opacity |
| Shape | radius, border width, shadows/effects |
| Content | labels, order, icons, empty/loading/error if in frame |
| Interaction | hover/pressed/disabled/focus if shown in Figma |
| Density | spacing rhythm vs neighbors **inside** the target only |

Visual parity is required. Token or name mismatches count as diffs only when
they change rendered appearance or block correct implementation of a visual
value.

Format:

```markdown
## Diff map — pass N

| # | Property | Figma | Code | Fix |
| --- | --- | --- | --- | --- |
| 1 | … | … | … | … |
```

If the map is empty and blockers are empty → **Report clean**.
If the map is non-empty → fix all implementable rows before the next map.

### 5. Fix

Apply all **independent** mapped fixes for the locked target in one batch.
If a fix depends on measuring a prior fix, split into ordered batches and
re-map after each batch.

- Always use existing project tokens / primitives when they match the Figma
  value; hard-code only when no matching token exists.
- Never refactor unrelated code.
- Never “improve” beyond Figma for this run.

### 6. Re-map (mandatory)

First-pass reads miss spacing, type, color, radius, alignment, states, and
density. After each fix batch, prove parity with a fresh pass — never memory
of the prior map:

1. Re-inspect Figma for the **same** locked node (fresh screenshot/metadata).
2. Re-inspect code / rendered UI.
3. Write a **new** diff map (pass N+1). Never reuse the previous map as proof.

If differences remain → fix again → re-map again.
Stop looping only when pass N reports **zero** rows.

### 7. Report clean

When the map is empty and no blockers affect appearance:

```markdown
## Figma Police — clean

- Target (Figma): …
- Target (code): …
- Passes run: N
- Remaining known blockers: none | [list]
```

If blockers remain (missing asset, unsupported CSS, unknown token), list them
and ask how to resolve. Never mark clean while blockers affect the locked
target’s appearance.

## Anti-patterns

- One-shot “I matched Figma” without a second empty map
- Fixing only the issues the user already pointed out
- Expanding scope to “while I’m here” polish
- Trusting the first Figma skim over measured values / screenshots
- Declaring parity from code reading alone with no visual check when tools
  allow one
