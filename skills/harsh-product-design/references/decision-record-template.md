# Decision record template

Use this shape for every rule written into a project's
`docs/product-design/references/`.

```markdown
## rule/{stable-id}
Status: accepted | proposed
Scope:
Rule:
Why:
Exceptions:
Source: {path or commit}
Bad example:
Good example:
```

## Status rules

- `proposed` — Learn / Auto-learn candidates; awaiting human confirmation
- `accepted` — human-approved and evidenced in shipped code or explicit decision
- Never auto-promote `proposed` → `accepted`

## Writing rules

- Observable only — no vague adjectives (`clean`, `polished`, `intuitive`)
- Prefer the narrowest correct file (`surfaces.md` vs `product-judgment.md`)
- Route to existing `.cursor/rules/*.mdc` instead of restating them
- One-offs stay in `coverage-gaps.md` until evidence repeats
