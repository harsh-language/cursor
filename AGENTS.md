# AGENTS.md

This repo is the source of truth for personal Cursor skills and cheat sheets.

## Skills

- Create and edit skills only under `skills/<skill-name>/SKILL.md` in **this** repo.
- Never treat `~/.cursor/skills` as a separate location — on this machine it is a symlink to `skills/` here.
- When the user asks to make a skill, put it in this repo and add a row to the Skills table in `README.md`.

### Naming prefixes

| Prefix | Purpose |
|--------|---------|
| `harsh-*` | Personal workflow skills (git, Figma, product design, cheatsheets) |
| `ek-*` | Emil Kowalski motion/design-eng skills ([emilkowalski/skills](https://github.com/emilkowalski/skills)) |
| `ek2-*` | Full design-engineering craft suite (animations.dev course) |
| (other) | External skills kept under their upstream folder name (e.g. `i-have-adhd`) |

### Invocation

- Users invoke skills by **folder name**: `/ek2-color`, `/harsh-git-sync`, etc.
- **`name:` in frontmatter must match the folder name exactly** — Cursor rejects mismatches.
- **Quote `description:` values** when they contain colons or special characters — unquoted YAML breaks discovery.
- `ek-review-animations`, `ek-pick-ui-library`, and `i-have-adhd` have `disable-model-invocation: true` — manual slash invoke only (ADHD style is still always-on via `rules/i-have-adhd.mdc`).

When renaming a skill folder, always update `name:` in `SKILL.md` to match.

### Skill groups

- **Workflow** — `harsh-*` (10 skills): git loop, Figma parity, product design, cheatsheets, Paper sync
- **Motion** — `ek-*` (8 skills): animation taste, review, audit, vocabulary, Apple design, library picking, prototyping
- **Design engineering** — `ek2-*` (15 skills): typography, color, surfaces, forms, performance, UI review, and more
- **Communication** — `i-have-adhd` (1 skill): ADHD-shaped output; always-on via `rules/i-have-adhd.mdc` (`alwaysApply: true`)

When adding skills from external sources, keep the source prefix (`ek-`, `ek2-`) or the upstream folder name to avoid name collisions.

### Always-on rules

Behavioral “always / never” preferences that must load every chat live in `rules/*.mdc` with `alwaysApply: true` (same pattern as `search-verification.mdc`). Skills alone are not always-on when they set `disable-model-invocation: true`.
