# GitHub Remote Setup

Load when `repo_setup.github_remote` is `yes` and `repo_setup.github_setup` is `pending`.

Runs **after §9 Technology Stack** is complete (or when resuming with a pending GitHub setup). Guide the user step by step — do not silently run all commands unless the user asks you to.

## Preconditions

- User opted in during §9 (`github_remote: yes`)
- Local git exists (`repo_setup.local_git: complete`). If missing, run Phase 0 project bootstrap first (`create_project` MCP or `git init` in the project root).
- §9 is written to blueprint with version-control choice recorded

## Guide flow (one step per turn unless user asks to batch)

### Step 1 — Confirm local git

    ```text
Local git is ready. Next we'll connect this project to GitHub.
Is the GitHub CLI (`gh`) installed on your machine?
```

If unknown, run `gh --version` and report. If missing, offer:
- Install: https://cli.github.com/
- Or manual setup via github.com (create empty repo → add remote URL)

### Step 2 — Auth check

Run `gh auth status` (or ask user to confirm they're logged in).

If not authenticated:
```text
Run: gh auth login
Follow the browser prompt, then tell me when you're done.
```

Wait for user confirmation before continuing.

### Step 3 — Repo visibility and owner

Ask (single turn):
```text
GitHub repo for [product_name]:
- Personal account or organization?
- Public or private?
```

Defaults if user doesn't care: personal account, **private**.

### Step 4 — Create remote and connect

Preferred (with `gh`):

```bash
gh repo create <repo-name> --private --source=. --remote=origin --push
```

Adjust flags per user choice (`--public`, `--org <name>`).

If user prefers manual:
1. Create empty repo on github.com (no README if local has commits)
2. `git remote add origin git@github.com:<owner>/<repo>.git`
3. `git branch -M main` (if needed)
4. `git push -u origin main`

Run commands only with user approval at each step, or when they say "go ahead."

### Step 5 — Verify and record

After success:
- `git remote -v` — confirm origin URL
- Write to blueprint §9: remote URL, visibility, owner
- Update `blueprint-status.yaml`:

```yaml
repo_setup:
  github_remote: yes
  local_git: complete
  github_setup: complete
  github_url: https://github.com/<owner>/<repo>
```

If user skips or defers:
```yaml
github_setup: skipped  # or deferred — note reason in Outstanding Questions
```

### Step 6 — Continue blueprint

After `github_setup` is `complete` or `skipped`, proceed to §10 (UI block) or next incomplete section. Do not block blueprint elicitation on GitHub unless user wants to finish setup first.

## Resume

| State | Action |
|-------|--------|
| `github_remote: yes`, `github_setup: pending` | Resume this guide from Step 1 |
| `github_remote: no` | `github_setup: not_requested` — never run this file |
| `github_remote: existing` | Confirm remote URL, set `github_setup: complete`, record URL in §9 |

## Errors

- **Repo name taken** — suggest alternate or let user pick name
- **No commits** — guide first commit before push
- **Auth fails** — re-run `gh auth login`; do not store tokens in blueprint or yaml
