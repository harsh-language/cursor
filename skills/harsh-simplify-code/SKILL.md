---
name: harsh-simplify-code
description: >-
  Pre-PR branch simplification forked from ce-simplify-code. Auto-fixes low-risk
  cleanup; asks before changes that could drift Figma UI or feature behavior.
  Triggers: harsh-simplify-code, pre-PR simplify, safe simplify.
argument-hint: "[blank for branch diff vs main, or name files/dirs to scope]"
disable-model-invocation: true
---

# harsh-simplify-code

Safer fork of `ce-simplify-code`. Same scope, three reviewers, and verification — but findings on UI/functionality-risky checkpoints require **user confirmation** before any edit. Low-risk cleanup applies automatically.

Prioritize readable, explicit code over compact solutions. Fewer lines is not the goal.

Load [checkpoint-policy.md](references/checkpoint-policy.md) at Step 2. Example policy validation: [sanity-check-u1-homepage.md](references/sanity-check-u1-homepage.md).

## Step 0: Initialize

1. Resolve branch name (`git branch --show-current`) for scope context only.
2. Load project rules when present:
   - `.cursor/rules/harsh-simplify-freeze.mdc` — **durable keep-as-is decisions** (see Decision freeze)
   - `.cursor/rules/figma-design-tokens.mdc`
   - `.cursor/rules/component-layering.mdc`
   - `.cursor/rules/button-content-alignment.mdc`
   - `.cursor/rules/scroll-fade.mdc`

## Step 1: Identify scope

Resolve the simplification scope in this order:

1. **If the user explicitly named a scope** (a file, a directory, "the function I just wrote", "the changes from this morning"), use that scope. Treat user-named scope as authoritative — do not widen it.
2. **Otherwise, in a git repository**, default to the diff between the current branch and its base branch (e.g., `git diff origin/main...` or against the configured upstream). This covers the common case of "simplify everything I've added on this feature branch before opening a PR." If the branch has no upstream or base ref, fall back to staged + unstaged changes (`git diff HEAD`).
3. **Outside a git repository or when no diff is available**, review the most recently modified files mentioned by the user or edited earlier in this conversation.

If none of the above produces a non-empty scope, stop and ask the user what to simplify rather than guessing.

## Step 2: Launch 3 review agents in parallel

Spawn the three reviewer agents below in a single message via the platform's subagent dispatch primitive — `Agent`/`Task` in Claude Code, `spawn_agent` in Codex — where available; otherwise run the work inline or serially. Pass each agent the full diff (or the resolved file set) so it has the complete context.

**Model selection.** Use the platform's mid-tier model for these reviewers when the current harness exposes a known override. In Claude Code this is the Sonnet class; in Codex use the current mini/mid-tier model exposed by `spawn_agent` when known. On platforms where the model-override parameter is unavailable or the model name is unknown or unrecognized, omit the override -- a working pass on the parent model beats a broken dispatch.

**Permission mode.** Omit the `mode` parameter on the dispatch call so the user's configured permission settings apply.

After all reviewers return, **tag each finding** with checkpoint ID and disposition (`auto` | `ask`) per [checkpoint-policy.md](references/checkpoint-policy.md). False positives: tag `skipped`, do not apply.

**Decision freeze (before Step 3).** Match each new finding against `.cursor/rules/harsh-simplify-freeze.mdc` using **`checkpoint ID + file path`** (and pattern column when helpful):

| Freeze rule match | Action |
|-------------------|--------|
| Same ID + file; pattern still present in code | Tag `frozen` — do **not** re-ask or auto-fix |
| Same ID + file; pattern removed or refactored | Tag `ask` — freeze stale; user may remove rule row |
| No match | Proceed with normal `auto` / `ask` tagging |

When uncertain whether the frozen pattern still applies, re-ask rather than silently freezing.

### Agent 1: Code Reuse Reviewer

For each change:

1. **Search for existing utilities and helpers** that could replace newly written code. Look for similar patterns elsewhere in the codebase — common locations are utility directories, shared modules, and files adjacent to the changed ones. → **R1**
2. **Flag any new function that duplicates existing functionality.** Suggest the existing function to use instead. → **R2**
3. **Flag any inline logic that could use an existing utility** — hand-rolled string manipulation, manual path handling, custom environment checks, ad-hoc type guards, and similar patterns are common candidates. → **R3**
4. **Flag diff code that reimplements a language standard-library or runtime primitive** — a hand-written routine the built-in stdlib/runtime API already provides (e.g., a manual array-dedup loop where the language ships a set-based idiom, a hand-rolled deep-clone/deep-merge where the runtime has one). Suggest the built-in **only when it is behavior-equivalent** for the inputs actually in play. Do not propose swaps that change behavior or UX: native UI controls (e.g., a custom date picker to `<input type=date>`), locale/`Intl`-dependent formatting, sort-stability assumptions, and serialization edge cases differ from their hand-rolled versions and are out of scope for a behavior-preserving pass. → **R4**

### Agent 2: Code Quality Reviewer

Review the same changes for hacky patterns:

1. **Redundant state**: state that duplicates existing state, cached values that could be derived, observers/effects that could be direct calls → **Q1**
2. **Parameter sprawl**: adding new parameters to a function instead of generalizing or restructuring existing ones → **Q2**
3. **Copy-paste with slight variation**: near-duplicate code blocks that should be unified with a shared abstraction → **Q3**
4. **Leaky abstractions**: exposing internal details that should be encapsulated, or breaking existing abstraction boundaries → **Q4**
5. **Stringly-typed code**: using raw strings where constants, enums (string unions), or branded types already exist in the codebase → **Q5**
6. **Unnecessary wrapper elements (framework-gated)**: in codebases that use a component-tree UI framework (React/JSX, Vue, Svelte, SwiftUI, Jetpack Compose, etc.), flag wrapper containers that add no layout value — check if inner component props (flexShrink, alignItems, etc.) already provide the needed behavior. Skip this rule entirely on codebases without such a framework. → **Q6**
7. **Nested conditionals**: ternary chains (`a ? x : b ? y : ...`), nested if/else, or nested switch 3+ levels deep — flatten with early returns, guard clauses, a lookup table, or an if/else-if cascade → **Q7**
8. **Unnecessary comments**: comments explaining WHAT the code does (well-named identifiers already do that), narrating the change, or referencing the task/caller — delete; keep only non-obvious WHY (hidden constraints, subtle invariants, workarounds) → **Q8**
9. **Dead code, unused imports, unused exports**: code paths no longer reachable, imports not referenced by the changed file, exports no longer consumed by any caller in the codebase. To verify "unused" across the codebase, prefer the project's existing unused-import/dead-code linter if configured (ESLint `no-unused-vars` / `unused-imports`, `knip`, `ruff F401`, `tsc --noEmit --noUnusedLocals`, `golangci-lint unused`, etc.). Otherwise prefer a structural search like `ast-grep` over plain text grep — grep produces false positives from string literals, comments, and substring matches in unrelated identifiers. Account for re-exports (`export * from`, barrel files), dynamic imports (`import()`, `require()`, template-string imports), and framework-specific exports (Next.js page exports, React Server Components, decorators). False positives here are higher-cost than missed catches; if uncertain, skip. → **Q9**

**Also hunt (extra — see checkpoint-policy T1–T3):**

- Inline `style={{}}` or dynamic style props on View/Text → **T1**
- Literals where project design tokens exist → **T2**
- Shadow/gradient copies outside theme helpers → **T3**

**Balance — avoid over-simplification.** Every flag above has a failure mode in the opposite direction; fewer lines is not the goal, faster comprehension is. Do not inline a helper that gives a concept a name, merge unrelated logic into one function, or remove an abstraction that exists for testability/extensibility or whose purpose you haven't confirmed is obsolete (check `git blame` for the original intent). If a proposed change would be longer or harder to follow than the original, don't flag it.

On `ask` items (especially Q3, Q6, T1–T3), prefer **extract to named variant + contract test** over blind merge when proposing fixes.

### Agent 3: Efficiency Reviewer

Review the same changes for efficiency:

1. **Unnecessary work**: redundant computations, repeated file reads, duplicate network/API calls, N+1 patterns → **E1**
2. **Missed concurrency**: independent operations run sequentially when they could run in parallel → **E2**
3. **Hot-path bloat**: new blocking work added to startup or per-request/per-render hot paths → **E3**
4. **Recurring no-op updates**: state/store updates inside polling loops, intervals, or event handlers that fire unconditionally — add a change-detection guard so downstream consumers aren't notified when nothing changed. Also: if a wrapper function takes an updater/reducer callback, verify it honors same-reference returns (or whatever the "no change" signal is) — otherwise callers' early-return no-ops are silently defeated → **E4**
5. **Unnecessary existence checks**: pre-checking file/resource existence before operating (TOCTOU anti-pattern) — operate directly and handle the error → **E5**
6. **Memory**: unbounded data structures, missing cleanup, event listener leaks → **E6**
7. **Overly broad operations**: reading entire files when only a portion is needed, loading all items when filtering for one → **E7**

## Step 3: Fix issues (split auto / ask)

Wait for all three agents to complete. Aggregate findings.

### 3a — Auto-apply

Apply all `auto`-tagged findings immediately (not tagged `frozen`).

Before each fix, confirm it preserves behavior: same output for every input, same error behavior, and same side effects and ordering. If a fix can't clear that test, skip it.

**Never simplify away a safety check.** Input validation at trust boundaries, error handling that prevents data loss, security checks (authorization, escaping, sanitization), and accessibility affordances are not removable boilerplate — preserve them even when a finding frames them as redundant or inline-able. Code that drops one of these is not simpler, it is unfinished. If a proposed simplification would thin or remove one, skip it.

### 3b — Ask loop

For each `ask`-tagged finding **not** tagged `frozen`, **one question per turn**:

- Show: checkpoint ID, file, snippet, proposed change
- Options: **apply** / **keep as-is** / **extract to named rule + test** (preferred for Q3, Q6, T1–T3)
- Do not batch multiple ask items in one question

Log `frozen` matches in the summary (e.g. "1 frozen from harsh-simplify-freeze rule") — no user turn consumed.

### 3c — Apply confirmed

Apply only user-confirmed ask items.

On **keep as-is**, append a row to `.cursor/rules/harsh-simplify-freeze.mdc` (Frozen patterns table): checkpoint ID, file, pattern snippet, reason. Commit with the branch.

**Never auto-apply any `ask` checkpoint.**

## Step 4: Verify behavior is preserved

The premise of this skill is that simplification preserves exact functionality. After applying fixes:

**Run typecheck and lint over the full project.** They are usually fast and catch the most common simplification regressions — broken imports, unused exports, dropped type narrowings, dead code other modules still reference.

**Run tests:**
- Run tests scoped to the changed paths. CI runs the full suite on PR — this local check is a fast signal, not the final guarantee. Match scope to blast radius; a 3-line simplification doesn't warrant a 20-minute test run.
- Broaden scope when the change has obvious wide reach — e.g., a heavily-imported utility was rewritten, or Agent 2's consolidation/dedup fixes modified shared code. This is a judgment call about ripple risk, not a mechanical rule.
- If the test runner has no scoping mechanism, run the full suite.

Surface any failure clearly with the failing check name and the relevant output. Do not relax assertions, weaken type signatures, or skip tests to make checks pass — that defeats the "preserves functionality" guarantee. Either fix the underlying break introduced by simplification, or revert the specific change that caused the regression.

If no test suite, lint, or typecheck is configured, state that explicitly in the summary; do not silently skip verification.

## Step 5: Summarize

Briefly summarize what was good vs improved and fixed, including which checks were run and their results. If there were no findings to act on, confirm the code didn't require any changes.

**Quantify the impact by dimension.** Report fixes per dimension, skipped false positives, new freeze rows added, frozen-from-rule count, and verification outcome.

Do not headline a net-lines-removed figure.

If freeze rule has entries, note: **ce-simplify not recommended** for flagged UI paths.

## Hard stops

- Never auto-apply any `ask` checkpoint (R1–R3, Q1, Q3, Q6, Q7, E3, T1–T3)
- Never edit `src/theme/tokens.ts` by hand
- Never change `typography.*` token choice without ask confirmation
- Never merge similar UI without a named variant rule (+ contract test if shared primitive)
- Never delete symbols on "looks unused" — structural verify or skip
- One user question per turn during ask loop (`frozen` findings do not count)
- On keep-as-is: append to `.cursor/rules/harsh-simplify-freeze.mdc`
- Do not invoke `ce-simplify-code` inside this skill
