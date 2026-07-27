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

### Invocation

- Users invoke skills by **folder name**: `/ek2-color`, `/harsh-git-sync`, etc.
- **`name:` in frontmatter must match the folder name exactly** — Cursor rejects mismatches.
- **Quote `description:` values** when they contain colons or special characters — unquoted YAML breaks discovery.
- `ek-review-animations` and `ek-pick-ui-library` have `disable-model-invocation: true` — manual only.

When renaming a skill folder, always update `name:` in `SKILL.md` to match.

### Skill groups

- **Workflow** — `harsh-*` (9 skills): git loop, Figma parity, product design, cheatsheets
- **Motion** — `ek-*` (8 skills): animation taste, review, audit, vocabulary, Apple design, library picking, prototyping
- **Design engineering** — `ek2-*` (15 skills): typography, color, surfaces, forms, performance, UI review, and more

When adding skills from external sources, keep the source prefix (`ek-`, `ek2-`) to avoid name collisions.
