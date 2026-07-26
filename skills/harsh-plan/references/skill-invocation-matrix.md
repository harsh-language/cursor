# Compound Engineering Skills

Load only when invoking. Meta doc is source of truth; merge only after user confirms.

## Phase 1 (one per turn, §1–4 gaps)

| Trigger | Skill |
|---------|-------|
| Unclear goals, users, metrics | `ce-strategy` |
| Multiple directions, prioritization | `ce-ideate` |
| Incomplete requirements, ambiguous flows | `ce-brainstorm` |

## Phase 3 (stress-test)

`ce-brainstorm` — review-only: stress-test meta doc for gaps, edge cases, and contradictions. Update `blueprint.md` in place; no separate requirements doc. No `ce-plan` or `ce-work`.

## Phase 4

`ce-doc-review` on blueprint.md. Resolve major findings before Stage 0 approval.

## Phase 5 (Stage 0 complete)

Load [phase-5-handoff.md](phase-5-handoff.md). Map Stage 1+ into the same meta doc. When the user starts any stage after Stage 0, invoke `ce-plan` for **that stage only**. `ce-plan` post-generation menu handles `ce-work` / done for now.

## Stage 0 hard stop

Do not run `ce-plan` or `ce-work` during elicitation (Phases 0–4 / Stage 0). Never run a whole-app `ce-plan`.
