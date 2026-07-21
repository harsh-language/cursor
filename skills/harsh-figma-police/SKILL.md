---
name: harsh-figma-police
description: >-
  Enter Figma Police mode: lock a specific UI target from a shared Figma link,
  map every visual difference vs the implemented code, fix all diffs, then
  re-diff and fix in a loop until Figma and code match. Use when the user says
  harsh-figma-police, Figma Police, police mode, Figma parity loop, or wants
  Cursor to stop missing Figma details after the first pass.
disable-model-invocation: true
---

# harsh-figma-police

You are in **police mode**. Figma is the source of truth for the locked UI
target. Do not stop after one pass. Diff → fix → re-diff until no differences
remain.

First-pass Figma reads are unreliable. Assume the initial interpretation will
miss spacing, type, color, radius, alignment, states, and density. Prove parity
with evidence, not memory of the first look.

## Hard rules

1. **One target.** Work only on the specific UI item the user named (or
   confirmed). Do not widen to neighboring screens, pages, or components.
2. **Ask when unclear.** If you do not know which node/frame in Figma **or**
   which file/component in code, stop and ask. Do not guess.
3. **Figma wins.** Prefer design values from Figma (tokens, measured layout,
   screenshots) over existing code when they conflict for the locked target.
4. **Loop until clean.** After every fix batch, re-map diffs from scratch.
   Exit only when a full re-map finds **zero** differences for the target.
5. **No silent “close enough.”** Record every mismatch. Fix every mismatch
   that is implementable in the current stack. If something cannot be matched
   (missing asset, unsupported CSS, unknown token), list it explicitly and ask
   — do not pretend it matches.

## Workflow

Copy and track this checklist:

```
Figma Police:
- [ ] 1. Lock target (Figma + code)
- [ ] 2. Inspect Figma evidence
- [ ] 3. Inspect code / rendered UI
- [ ] 4. Map differences
- [ ] 5. Fix all mapped diffs
- [ ] 6. Re-map (fresh pass)
- [ ] 7. Repeat 5–6 until map is empty
- [ ] 8. Report clean
```

### 1. Lock target

Resolve, in order:

1. Figma URL / file / node from this conversation (or the user’s latest link).
2. The specific UI part (frame, component, state, breakpoint).
3. The matching code surface (route, component file, story, CSS module).

If any of those three is missing or ambiguous → **ask once**, then wait.

Do not proceed to mapping until both sides are locked.

### 2. Inspect Figma

Use available Figma MCP / console tools. Prefer:

- Node metadata, layout, text, fills, strokes, effects, radius, padding, gap
- Variables / tokens bound to the node when available
- Screenshots of the exact target (and relevant variants/states)

Load Figma prerequisite skills before write-oriented Figma tools when required
by those skills. For police mode, **read/inspect first**; only mutate code
unless the user asked to change the Figma file.

Capture concrete values (px, rem-equivalent, hex/rgba, font size/weight/
line-height/letter-spacing, alignment). Do not summarize vaguely
(“looks tighter”).

### 3. Inspect code

Find the implemented counterpart for the locked target. Prefer:

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

Format:

```markdown
## Diff map — pass N

| # | Property | Figma | Code | Fix |
| --- | --- | --- | --- | --- |
| 1 | … | … | … | … |
```

If the map is empty → go to **Report clean**.
If the map is non-empty → fix **all** rows before the next map.

### 5. Fix

Apply every mapped fix for the locked target in one batch when safe.

- Prefer existing project tokens / primitives over hard-coded one-offs.
- Do not refactor unrelated code.
- Do not “improve” beyond Figma for this run.

### 6. Re-map (mandatory)

After fixes:

1. Re-inspect Figma for the **same** locked node (fresh screenshot/metadata).
2. Re-inspect code / rendered UI.
3. Write a **new** diff map (pass N+1). Do not reuse the previous map as proof.

If any differences remain → fix again → re-map again.

Stop looping only when pass N reports **zero** rows.

### 7. Report clean

When the map is empty:

```markdown
## Figma Police — clean

- Target (Figma): …
- Target (code): …
- Passes run: N
- Remaining known blockers: none | [list]
```

If blockers remain (unmatched assets, impossible CSS, missing tokens), list
them and ask how to resolve. Do not mark clean while blockers affect the
locked target’s appearance.

## Anti-patterns

- One-shot “I matched Figma” without a second empty map
- Fixing only the issues the user already pointed out
- Expanding scope to “while I’m here” polish
- Trusting the first Figma skim over measured values / screenshots
- Declaring parity from code reading alone with no visual check when tools
  allow one
