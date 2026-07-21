---
name: harsh-git-sync
description: >-
  Mirror local git state to a private GitHub repo (glorified save file). Creates
  the repo after confirming the name if needed; pushes every local branch;
  reconciles merged PRs and deletes remote branches gone locally. Use when the
  user says harsh-git-sync, sync to github, save to github, or push local as
  mirror.
disable-model-invocation: true
---

# harsh-git-sync

Make GitHub match this Mac. Local is source of truth. **Do not open new PRs** as the workflow — only reconcile existing ones so cloud status matches local.

Load shared rules: [../harsh-git/references/shared.md](../harsh-git/references/shared.md)

## Goal

GitHub is a glorified save file: same commits, same branches, merged/closed PRs where local already merged that work, no leftover remote branches that do not exist locally.

## Step 0 — Inspect once

```bash
git rev-parse --is-inside-work-tree
git remote -v
git branch --format='%(refname:short)'
gh auth status
```

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

## Done when

- Every local branch is on `origin`
- Open PRs for work already on local `main` are merged/closed on GitHub
- Remote-only branches (except trunk) are gone
- Brief summary of what was pushed, merged/closed, and deleted
