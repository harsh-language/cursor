# Checkpoint policy

Each reviewer finding gets a checkpoint ID and a disposition: **`auto`** (apply without asking) or **`ask`** (stop for user confirmation).

Policy is **checkpoint-based, not contextual** — conservative by design. If a checkpoint can drift Figma UI or feature behavior, always `ask`, even when a specific finding looks safe.

Load [checkpoint-policy.md](checkpoint-policy.md) at Step 2 after reviewers return findings.

## Ask first

| ID | Source | Checkpoint | Why ask |
|----|--------|------------|---------|
| **R1** | Reuse | Replace new code with existing utility | Can swap implementation and change behavior |
| **R2** | Reuse | Remove duplicate function → use existing | Same |
| **R3** | Reuse | Inline logic → existing utility | Can alter edge-case behavior |
| **Q1** | Quality | Redundant state → derive | Can break interaction timing / pressed states |
| **Q3** | Quality | Copy-paste → shared abstraction | Primary font/layout drift vector |
| **Q6** | Quality | Remove wrapper Views/components | Primary alignment drift vector |
| **Q7** | Quality | Flatten nested conditionals | Can change branch order / edge cases |
| **E3** | Efficiency | Hot-path bloat (startup / per-render) | Can change when work runs → subtle UX shifts |
| **T1** | Quality (extra) | Inline / dynamic styles on View/Text | Design-token and pressed-state drift |
| **T2** | Quality (extra) | Magic numbers vs design tokens | Spacing/color drift from Figma |
| **T3** | Quality (extra) | Duplicated shadows/gradients outside theme helpers | Visual drift |

## Auto — cleanup only

| ID | Source | Checkpoint |
|----|--------|------------|
| **R4** | Reuse | Stdlib/runtime swap (behavior-equivalent only; no UX-changing swaps) |
| **Q2** | Quality | Parameter sprawl |
| **Q4** | Quality | Leaky abstractions |
| **Q5** | Quality | Stringly-typed → existing unions/constants |
| **Q8** | Quality | Unnecessary WHAT comments (preserve WHY comments) |
| **Q9** | Quality | Dead code / unused imports / exports (structural verify; skip if uncertain) |
| **E1** | Efficiency | Redundant work, N+1, duplicate reads/calls |
| **E2** | Efficiency | Missed parallelization |
| **E4** | Efficiency | No-op state/store updates → change guards |
| **E5** | Efficiency | Unnecessary existence pre-checks |
| **E6** | Efficiency | Memory leaks / missing cleanup |
| **E7** | Efficiency | Overly broad reads/loads |

## Extra hunts (T1–T3)

Add to Agent 2 (Quality) review pass. All T findings are **`ask`**.

### T1 — Inline / dynamic styles

- `style={{ ... }}` on `View`, `Text`, or animated wrappers
- Dynamic style props: `{ color }`, `pressed && styles.x`, conditional style arrays that change layout or typography
- Mixed `typography.*` at similar call sites (`para1` vs `para2`)

### T2 — Magic numbers vs tokens

When project has design-token rules (e.g. `.cursor/rules/figma-design-tokens.mdc`):

- Literals for spacing, radius, border width where `spacing['s-*']` / `radii['r-*']` exist
- Raw hex/rgb not from `colors[...]`
- `opacity: 0.5` and similar literals — confirm vs token pattern
- Never edit `src/theme/tokens.ts` by hand — flag for Figma re-export if token missing

### T3 — Duplicated theme visuals

- Shadow copies outside shared helpers (e.g. `src/theme/shadow.ts`)
- Scroll-fade / gradient copies outside `src/theme/scrollFade.ts` or `ScrollFadeView`
- Duplicate `LinearGradient` stop definitions inline

## Q-balance on ask items

When proposing a fix for `ask` findings (especially Q3, Q6, T1–T3):

1. Prefer **extract to named variant + contract test** over blind merge
2. Follow project component-layering rules when present (`.cursor/rules/component-layering.mdc`)
3. For button alignment: extend `getButtonContentLayout` pattern, not per-call-site flex overrides
4. If variants differ on purpose, encode the matrix in a pure helper — do not collapse into one block

## Tagging findings

Each finding record:

```
id: Q3
file: src/components/PrimaryButton.tsx
disposition: ask
snippet: ...
proposed: Extract shared label row into ...
```

If uncertain whether a symbol is unused (Q9), tag `skipped` — do not auto-delete.

## Decision freeze (re-runs)

Load `.cursor/rules/harsh-simplify-freeze.mdc` at Step 0. Match key: **checkpoint ID + file path** (use Pattern column when needed).

- Rule row matches + pattern still in code → `frozen` (honor, no re-ask)
- Rule row matches + pattern gone → `ask` (stale freeze — remove rule row after user confirms)
- No rule row → normal `auto` / `ask`

On **keep as-is**, append a row to the freeze rule in the same change.

## Project rules (load at Step 0 when present)

| Rule file | Governs |
|-----------|---------|
| `.cursor/rules/harsh-simplify-freeze.mdc` | Durable keep-as-is decisions |
| `.cursor/rules/figma-design-tokens.mdc` | T2 hunts |
| `.cursor/rules/component-layering.mdc` | Q3 extraction tier, contract tests |
| `.cursor/rules/button-content-alignment.mdc` | Q3/Q6 button layout |
| `.cursor/rules/scroll-fade.mdc` | T3 gradient/fade copies |
