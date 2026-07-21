# harsh-git shared rules

Load this from any `harsh-git-*` skill before committing or pushing.

## Commit = save

- Never ask the user to stage files.
- Always commit with `git add -A` (respects `.gitignore` and git meta).
- If there is nothing to commit, say so and continue the skill’s next step.

```bash
git add -A
git status
git diff --cached
git log -5 --oneline
```

Write a short message focused on **why**, then commit via HEREDOC:

```bash
git commit -m "$(cat <<'EOF'
Explain why this change exists in 1–2 sentences.

EOF
)"
```

- Do **not** add AI co-author trailers unless the user asks.
- Do **not** update git config.

## Defaults

| Setting | Value |
|---------|--------|
| Default branch | `main` |
| New GitHub repos | **private** |
| Divergence | Local wins — push with `--force-with-lease` |
| Audience | Solo designer; no collaborator / PR-as-workflow theater |

## Auth

- If `gh` or git remote auth fails → ask the user; never invent credentials.
- Prefer `gh` for GitHub repo create, PR list/merge/close, and auth checks.

## Resolve current branch

```bash
git rev-parse --is-inside-work-tree
git branch --show-current
```

Treat `main` as the default trunk. If the only trunk is `master`, use that as trunk for this repo and say so once.
