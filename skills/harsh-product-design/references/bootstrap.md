# Bootstrap

Run when the project is missing `docs/product-design/`.

## Steps

1. Create the directory tree from [local-contract.md](local-contract.md).
2. Write stub files with a short header and **no accepted product rules yet**.
3. Propose the `AGENTS.md` trigger from [agents-trigger.md](agents-trigger.md).
   **Do not write `AGENTS.md` until the user confirms.**
4. Continue the user's requested work using:
   - the new stubs
   - code, `taxonomy.md`, and `.cursor/rules` when present
5. Let Auto-learn / Learn add only `status: proposed` entries afterward.

## Stub headers

Use this pattern for each empty reference file:

```markdown
# {Title}

Status: bootstrap stub
No accepted rules yet. Harvest with harsh-product-design Learn / Auto-learn;
keep new rules as `status: proposed` until a human accepts them.
```

## Stub contents checklist

| File | Initial content |
| --- | --- |
| `README.md` | Index + pointer to global skill `harsh-product-design` |
| `references/*.md` | Stub header above |
| `exemplars/` | Empty directory (or a short README: “add exemplars from shipped work”) |
| `coverage-gaps.md` | Stub header + note that gaps will be logged here |
| `lint-candidates.md` | Stub header + note that candidates use the lint decision tree |

## Do not

- Invent product conventions during bootstrap
- Mark any rule `accepted` during bootstrap
- Skip proposing the `AGENTS.md` trigger when the file exists and lacks it
