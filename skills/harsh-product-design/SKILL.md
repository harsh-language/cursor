---
name: harsh-product-design
description: >-
  Single entry point for product design and user-facing product implementation
  in any project repo. Use whenever work changes what a user sees, understands,
  chooses, or does: shaping flows; building or redesigning screens, sheets, and
  components (including when Figma is full, partial, or missing); reviewing
  diffs or UI behavior; improving copy, hierarchy, layout, interaction,
  accessibility, states, and resilience. Bootstraps docs/product-design/ when
  missing. Modes are internal — pick them automatically from the request; the
  user never names a mode. Not for backend-only work, config, docs, or tests
  with no shipped UI impact.
---

# Harsh Product Design

Make the interface correct for the user, the product, and the current repo.
Working code is not enough: choose the right interaction, make scope and
consequences clear, cover reachable states, and verify the real surface.

This skill is **global**. Product-specific decisions live in the project under
`docs/product-design/`. Process templates for this skill live in
`references/` beside this file.

## Operating Contract

- Start with the job, not the pixels.
- Define the outcome before the output.
- Use evidence, not taste.
- Separate facts from decisions.
- Treat shipped code as evidence, not automatic precedent.
- Choose the smallest coherent intervention.
- Decide before decorating.
- Design every reachable state.
- Verify the real surface.
- Keep one user-facing entry point.
- Never ask the user to name a mode. Select the mode yourself and report it.
- Never invent product rules for a blank project — bootstrap stubs, then Learn.

## Local base (always resolve first)

1. Resolve the project workspace root (the repo the user is working in).
2. Load [local-contract.md](references/local-contract.md).
3. If `docs/product-design/` is missing, follow [bootstrap.md](references/bootstrap.md).
4. If the local tree exists but `AGENTS.md` lacks the product-design trigger,
   propose the block from [agents-trigger.md](references/agents-trigger.md) —
   **do not write until the user confirms**.
5. Load applicable files under `docs/product-design/` for the chosen mode.

Local product truth (per project):

```
docs/product-design/
├── README.md
├── references/
│   ├── product-judgment.md
│   ├── interface-quality.md
│   ├── copy.md
│   ├── surfaces.md
│   ├── resilience.md
│   └── routing.md
├── exemplars/
├── coverage-gaps.md
└── lint-candidates.md
```

## Mode selection (internal)

Modes are agent-internal routing. The user invokes this skill once (or
`AGENTS.md` loads it). Infer the mode from the request verb and artifact —
do not ask which mode to use.

| Mode | Infer when the request… | Required behavior |
| --- | --- | --- |
| Shape | asks how something should work, designs a flow, or has unsettled UI | Frame the problem and evidence, then define the flow, states, acceptance criteria, risks, and open decisions. Do not edit unless asked. |
| Implement | asks to build, fix, improve, or make UI from Figma / siblings | Resolve material product decisions, then implement the smallest coherent end-to-end change. Run **Build mechanics**. Then run **Auto-learn**. |
| Review | asks to audit, critique, or report what's wrong | Inspect source and rendered evidence, then report prioritized findings. Do not edit unless asked. |
| Copy | asks only to fix labels, CTA text, or accessible names | Edit user-facing language, accessible names, and directly required JSX only. Report structural blockers without broadening scope. |
| Harden | asks to polish, production-ready, or handle edge cases | Preserve the settled product direction while fixing state, resilience, accessibility, and finish defects. Use **Build mechanics** only when composition is required. Then run **Auto-learn**. |
| Learn | asks to harvest decisions, update skill guidance, or run a full learn pass | Run the full learning loop over the codebase vs local references. Write only `status: proposed` into `docs/product-design/`. Never mark `accepted` without human approval. |

### Selection rules

1. Prefer the narrowest mode that satisfies the request.
2. A file, route, screenshot, or component identifies scope — it does not by
   itself authorize edits (default to Review when the verb is unclear).
3. If the request mixes verbs, pick one primary mode; do not ask the user to
   choose. Report the chosen mode in `Loaded:`.
4. Shape, Review, Copy, and standalone Learn must not run the full Build
   mechanics loop.
5. After every completed Implement or Harden run, always run **Auto-learn**
   on the surfaces touched — the user does not request Learn separately for
   that.

## Decision Authority

Resolve conflicts in this order:

1. The user's explicit goal and constraints.
2. Verified product behavior and current system truth in code.
3. Repository-canonical guidance: `AGENTS.md`, `taxonomy.md` (if present), and
   `.cursor/rules/*.mdc`.
4. Accepted decisions in the project's `docs/product-design/references/` and
   `docs/product-design/exemplars/`.
5. Verified adjacent shipped patterns in the same product area.
6. General interface heuristics.

## Workflow

### 1. Set scope and choose mode

Name the target surface. Select the mode from the table above. State the
chosen mode only in notes / `Loaded:` — never ask the user to name it.

### 2. Load product context

Before proposing UI, read:

- `AGENTS.md` (if present)
- `taxonomy.md` (if present)
- relevant `.cursor/rules/*.mdc`
- the product logic that determines mutations, permissions, validation, errors,
  and side effects
- applicable files under `docs/product-design/`

When mode is Implement (or Harden with composition), also follow **Build
mechanics** below.

### 3. Model the product decision

For Shape, Implement, Harden, full Review, or any material product/flow change,
write a compact internal brief covering:

- user
- job
- current behavior
- desired outcome
- success signal
- non-goals
- object
- scope
- action
- consequence
- reversibility
- permissions
- open decisions

### 4. Map the surface and states

Inventory only reachable states, including loading, empty, sparse, populated,
validation, error, permission, disabled, optimistic, stale, destructive, and
responsive variants.

### 5. Load routed references

Use `docs/product-design/references/routing.md` to decide which local files and
repo rules to load. Route to existing owners instead of copying their guidance.
Use [decision-record-template.md](references/decision-record-template.md) when
writing new rules.

### 6. Decide, then implement

For each non-mechanical change, be able to answer:

- What user problem does this solve?
- Why is this surface or component appropriate?
- What consequence must the interface communicate?
- Which evidence supports the decision?
- What is the smallest coherent change?

Then, for Implement (and Harden when composition is required), run **Build
mechanics**.

### 7. Verify

1. Confirm the primary job and acceptance criteria.
2. Run repository lint and test checks appropriate to the change.
3. Inspect relevant compact and wide viewports.
4. Exercise every materially changed reachable state.
5. Verify focus movement, loading behavior, and touch / pointer targets as
   relevant to the platform.
6. Test long content, large values, and constrained width. Add localization /
   RTL checks only when the product supports them.

### 8. Auto-learn (after Implement or Harden)

Always run after a completed Implement or Harden change. Scope: only the
surfaces and files this run touched — not a whole-repo harvest.

1. Diff the touched files against existing
   `docs/product-design/references/` and `docs/product-design/exemplars/`.
2. Collect raw observations (file path; commit if useful).
3. Classify each:
   - consistent with an existing rule → no write
   - candidate new rule → write `status: proposed` in the correct local
     reference
   - contradiction → log in `docs/product-design/coverage-gaps.md` and flag
     to the user
4. If nothing material → report `learn: none` and stop.
5. Never mark a new rule `accepted` without human confirmation.
6. Never promote a single one-off into a universal rule by itself.

Standalone full Learn (whole codebase vs `taxonomy.md` and local references)
remains available when the user asks to harvest or update skill guidance —
still selected automatically from that request, not named as a mode by the
user.

## Build mechanics (Implement; Harden when composition needed)

Compose from the app's existing design language. Figma wins where present; the
codebase fills gaps.

Shape, Review, Copy, and Learn must not run this loop.

### Steps

1. **Figma** — If a file/frame is linked, inspect it. Map what exists vs missing.
2. **Siblings** — Read `taxonomy.md` (if present) and nearby screens for shells
   and component homes.
3. **Compose** — Reuse existing project shells and primitives. Do not invent
   parallel components for one screen.
4. **Apply project rules** — Route to `.cursor/rules/*.mdc` via
   `docs/product-design/references/routing.md`. Do not restate those owners
   here.
5. **Tests** — When shared pure logic changes, add/update contract tests per
   the project's layering / testing rules.
6. **Call out guesses** — In the summary, list what was Figma-backed vs inferred
   from siblings/code.

### Do not

- Catalog or re-document individual component styles inside this skill — the
  code is the source of truth
- Add one-off colors, radii, type scales, or button styles when an existing
  pattern is close enough
- Fix shared primitives for a single-screen quirk — fix at the lowest true layer

## Routing (local)

Load these **project** references by need (paths under `docs/product-design/`):

| Need | Load |
| --- | --- |
| Product or flow decision | `references/product-judgment.md` + `references/surfaces.md` |
| Material visual change or full review | `references/interface-quality.md` |
| Copy or accessible names | `references/copy.md` + `references/surfaces.md` |
| Reachable states, empty/error/disabled behavior | `references/resilience.md` |
| Existing rule owner | `references/routing.md` |
| Implement/Harden composition | **Build mechanics** above + `references/routing.md` |

## Full Learn loop

Use when Auto-learn is too narrow (user asks to harvest decisions or update
guidance across the product). Same classification rules as Auto-learn. Write
only into `docs/product-design/`.

1. Diff the current codebase against:
   - existing files in `docs/product-design/references/`
   - existing files in `docs/product-design/exemplars/`
   - `taxonomy.md` build stage and screen map (if present)
2. Collect raw observations first, each with a source file and, when useful, a
   commit hash.
3. Classify each observation:
   - consistent with an existing rule
   - candidate new rule
   - contradiction
4. Separate collection from judgment. List observations before proposing any
   new rule text.
5. Write candidate guidance as `status: proposed` in the correct local
   reference file.
6. Log contradictions, missing standards, stale docs, and open questions in
   `docs/product-design/coverage-gaps.md`.
7. Never promote a single file or one-off into a universal rule by itself.
8. Never mark a new rule `accepted` without human confirmation.

## Reporting

Every run must report what was loaded so skill invocation is visible.

End the response with a `Loaded:` line that lists:

- mode chosen (agent-selected; user did not name it)
- whether `docs/product-design/` was bootstrapped or already present
- local references loaded from `docs/product-design/`
- `.cursor/rules/*.mdc` files loaded
- exemplars consulted
- whether **Build mechanics** ran
- whether an `AGENTS.md` trigger was proposed (pending user approval)
- learn outcome: `none` | `proposed N` | `coverage-gaps updated` | `full learn`

Every finding or recommendation must cite either:

- a `rule/{stable-id}` from the project's `docs/product-design/`
- a file path in the project repo
- a commit hash used as evidence
