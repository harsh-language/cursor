---
name: harsh-git-sync
description: Mirror local git to a private GitHub repo (refresh README, commit-all on the current branch, push all branches).
disable-model-invocation: true
---

# harsh-git-sync

Make GitHub match this Mac. Local is source of truth. **Do not open new PRs** as the workflow — only reconcile existing ones so cloud status matches local.

Load shared rules: [../harsh-git/references/shared.md](../harsh-git/references/shared.md)

## Goal

GitHub is a glorified save file: same commits, same branches, merged/closed PRs where local already merged that work, no leftover remote branches that do not exist locally. `README.md` stays an accurate summary of what’s in the repo.

## Step 0 — Inspect once

Prefer the project workspace root (e.g. the opened folder), not `$HOME`. Confirm with `pwd` and `git rev-parse --show-toplevel` before acting.

```bash
pwd
git rev-parse --show-toplevel
git rev-parse --is-inside-work-tree
git remote -v
git branch --format='%(refname:short)'
git status -sb
gh auth status
```

## Step 0a — Refresh README

Before save/mirror, update `README.md` at the repo root so it accurately summarizes the current repo. Do this every sync, even if other files look clean.

1. Inventory what’s actually here (at least):
   - `skills/*/SKILL.md` — name + one-line purpose from each skill’s frontmatter `description` or title
   - `cheatsheets/*` (if present) — filename + topic from the first heading or obvious purpose
   - Any other top-level folders/docs the README already claims to cover
2. Rewrite or patch `README.md` so inventories match reality (add new items, remove gone items, fix stale blurbs).
3. Set **Last updated** to today’s date (local).
4. Keep the README’s existing voice and structure when one already exists; don’t invent a new doc style. If there is no `README.md`, create a short one with purpose + inventories.
5. Do **not** ask the user to approve README wording unless something is ambiguous (e.g. two folders could both be “the” inventory).

## Step 0b — Commit-all on the current branch if dirty

If the working tree is dirty (staged, unstaged, or untracked files that `git add -A` would pick up) — including a README change from Step 0a — **commit-all on the current branch** (shared rules). Then continue bootstrap / mirror.

Never run `harsh-git-main` from this skill. Never checkout another branch. Never merge. Sync is a cloud save of whatever branch you are on; landing work onto `main` is a different button.

If the tree is clean, skip commit and continue.

## Bootstrap — not a git repo

1. Ask: set up git on this folder?
   - **No → stop entirely.** No further activity for this skill.
   - **Yes →** run `harsh-git` (read `~/.cursor/skills/harsh-git/SKILL.md`) then continue.
2. Continue at **Confirm repo name**.

## Bootstrap — git repo, no usable `origin`

1. Continue at **Confirm repo name**.

## Confirm repo name (required before any GitHub mutation)

1. Propose default name = current folder name.
2. Ask: use that name, or a different name? If different, take the user’s name.
3. **Do not create, push, or change GitHub until the name is confirmed.**
4. If `gh` is not authenticated → ask the user to fix auth; stop until it works.
5. Create a **private** repo with that name (user account unless the user specifies an org), add `origin`, then go to **Mirror**.

```bash
gh repo create <name> --private --source=. --remote=origin
```

If the repo already exists on GitHub but has no local remote, add `origin` to it instead of creating a duplicate — ask once if unclear.

## Mirror — `origin` exists

1. **Push every local branch** (local wins on divergence):
   ```bash
   git push --force-with-lease --set-upstream origin --all
   ```
   Also push tags if any exist: `git push --force-with-lease origin --tags`

2. **PR reconciliation** (match “already merged locally”):
   - List open PRs: `gh pr list --state open`
   - For each open PR whose head branch tip is already reachable from local `main` (`git merge-base --is-ancestor <head-sha> main`):
     - Merge or close it on GitHub so status is merged/closed (`gh pr merge <n>` preferred; if GitHub refuses because main already has the commits, `gh pr close <n>` with a short note that local already merged it)
   - Do **not** invent new PRs

3. **Branch reconciliation**:
   - List remote branches: `git ls-remote --heads origin`
   - Delete each remote branch that has **no** matching local branch
   - **Never** delete remote `main` (or the repo trunk)
   ```bash
   git push origin --delete <branch>
   ```

4. **Working tree must be clean.** If anything is still dirty (README leftover, sync-created files), commit-all on the current branch (shared rules) and push again (`git push --force-with-lease --set-upstream origin --all`). Repeat until `git status` is clean. Never leave the user with uncommitted sync edits.

## Done when

- `README.md` matches the current repo inventory
- Working tree is clean (README and any other sync edits are committed on the current branch)
- Every local branch is on `origin`
- Open PRs for work already on local `main` are merged/closed on GitHub
- Remote-only branches (except trunk) are gone
- Brief summary of what was pushed, merged/closed, deleted, and whether README changed
