# AGENTS.md trigger block

Propose this block when the project lacks a product-design trigger. **Do not
write until the user confirms.**

```markdown
## Product design

When shaping, editing, or reviewing user-facing UI, load the
`harsh-product-design` skill.

Applies to:
- screens and components
- copy, interaction, accessibility, and states
- build/edit work when Figma is full, partial, or missing

Skip:
- backend-only work with no user-visible effect
- config, docs, and tests with no UI impact

The skill selects its mode automatically from the request. Implement and
Harden include Build mechanics (Figma inspection, composition, contract-test
follow-through) and a post-change auto-learn pass that only proposes guidance
for human approval.

Local product decisions live in `docs/product-design/`.
```

## Placement

- Prefer inserting after the project map / taxonomy pointer if one exists.
- Otherwise place near other agent-facing workflow sections.
- Do not duplicate if an equivalent trigger already exists; update paths only
  when migrating from a project-local skill.

## Runtime note

The skill is installed from the personal Cursor kit (`~/Code/cursor/skills`,
exposed as `~/.cursor/skills`). Agents load it by skill name:
`harsh-product-design`.
