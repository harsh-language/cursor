# Compound Engineering Skills

Load only when invoking. Blueprint is source of truth; merge only after user confirms.

## Phase 1 (one per turn, §1–4 gaps)

| Trigger | Skill |
|---------|-------|
| Unclear goals, users, metrics | `ce-strategy` |
| Multiple directions, prioritization | `ce-ideate` |
| Incomplete requirements, ambiguous flows | `ce-brainstorm` |

## Phase 3 (stress-test)

`ce-brainstorm` — review-only: stress-test blueprint for gaps, edge cases, and contradictions. Update `blueprint.md` in place; no separate requirements doc. No `ce-plan` or `ce-work`.

## Phase 4

`ce-doc-review` on blueprint.md. Resolve major findings before approval.

## Phase 5 (on blueprint approval)

Load [phase-5-handoff.md](phase-5-handoff.md). When `auto_plan: true` (default), invoke `ce-plan` immediately with MVP scope from §18. `ce-plan` post-generation menu handles `ce-work` / done for now.

## Manual only (`auto_plan: false`)

User may invoke `ce-plan` from Phase 5 approve-only menu or later. Do not run `ce-plan` or `ce-work` during elicitation (Phases 0–4).
