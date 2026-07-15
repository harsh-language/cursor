# Agent guidance gate

Load at Step 6 of `harsh-simplify-code`. Project-only. Do not touch user-level Cursor rules or global skills.

Compare reality (code + stage + taxonomy) to meta docs. Propose changes. **Ask before writing.**

This gate **steers agents**. It is not `ce-compound` / `ce-compound-refresh` — do not write `docs/solutions` or treat this as a learning capture pass.

## Scope

| Check | Path |
|-------|------|
| Baseline facts | `AGENTS.md` |
| Behavioral rules | `.cursor/rules/*.mdc` |
| Process skills | `.cursor/skills/*/SKILL.md` |

Also use (read-only context): `taxonomy.md`, implementation plan / stage notes, existing freeze rule if present (`.cursor/rules/harsh-simplify-freeze.mdc`).

## Decision rule

| Signal | Put it in |
|--------|-----------|
| Repeated judgment (“always / never”) | Rule (`.cursor/rules/*.mdc`) |
| Stable project fact / pointer | `AGENTS.md` |
| Repeatable multi-step process | Project skill (`.cursor/skills/…`) |
| One-off / obvious from code / volatile | Skip — leave to chat |

**Do not** invent component inventories, style catalogs, or duplicate what the code already is.

## Always-apply vs scoped rules

- `alwaysApply: true` — universal project judgment (rare)
- `globs` — file-type or path-specific conventions
- Prefer scoped when the rule only matters for certain files

## Skills vs rules vs code

- Skill = process steps when doing a job
- Rule = standing or scoped instructions while working
- Code = source of truth for components / tokens / styles — never skill-ify the design system

## Proposal format

Use this table when proposing:

| # | Action | Target | Why | Draft |
|---|--------|--------|-----|-------|
| 1 | add | `.cursor/rules/example.mdc` | … | one-line content summary |
| 2 | update | `AGENTS.md` — Build stage | … | … |
| 3 | remove | `.cursor/rules/stale.mdc` | … | — |
| 4 | keep | `.cursor/skills/build-ui` | … | — |

Then ask: which items to apply? (batch approve / deny)

## Workflow (Step 6)

1. Read current meta — `AGENTS.md`, all `.cursor/rules/*.mdc`, all `.cursor/skills/*/SKILL.md`.
2. Read product truth — taxonomy / stage notes when present, conventions encoded in code for this run’s scope.
3. Diff — missing, stale, duplicated, over-scoped, or should be removed?
4. Propose the short table above.
5. Ask permission; write only approved items.
6. Summarize meta outcome (applied / skipped / none).

Keep rules concise (~under 50 lines, one concern). Keep `AGENTS.md` as facts/pointers, not behavior dumps.

## Out of scope

- Implementing product features
- Global / user Cursor settings (`~/.cursor` skills/rules outside the project)
- Rewriting the blueprint or full taxonomy unless the user asks
- Creating per-component documentation skills
- Re-opening frozen “keep as-is” code decisions unless the user asks
- Capturing solved-problem docs (`docs/solutions`) — use `ce-compound` for that
