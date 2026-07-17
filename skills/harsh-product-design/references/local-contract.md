# Local contract

Every project that uses `harsh-product-design` keeps product decisions here:

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

## File roles

| Path | Purpose |
| --- | --- |
| `README.md` | Human index; points at the global skill and this tree |
| `references/product-judgment.md` | Jobs, defaults, consequences, product objects |
| `references/interface-quality.md` | Hierarchy, reuse, composition quality (routes to rules) |
| `references/copy.md` | Labels, CTAs, accessible names |
| `references/surfaces.md` | Per-surface decisions for built screens/sheets |
| `references/resilience.md` | Loading, empty, sparse, error, disabled, destructive |
| `references/routing.md` | Task/surface → this repo's `.cursor/rules` and domain paths |
| `exemplars/` | Good/bad examples from shipped code + commits |
| `coverage-gaps.md` | Missing standards, contradictions, open questions |
| `lint-candidates.md` | Mechanical rules worth automating later (no linters yet) |

## Rules

- The global skill owns process. This tree owns product memory.
- Do not put product-specific rules in `~/.cursor/skills/harsh-product-design/`.
- Do not put the invokable `SKILL.md` inside `docs/product-design/`.
- Prefer routing to existing `.cursor/rules/*.mdc` over restating them here.
