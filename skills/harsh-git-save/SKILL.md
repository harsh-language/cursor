---
name: harsh-git-save
description: >-
  Fold local work into main: on main, commit-all; on a feature branch, optional
  harsh-simplify-code then commit-all, merge into main, delete the feature
  branch. Use when the user says harsh-git-save, save to main, merge branch to
  main, or land this work on main.
disable-model-invocation: true
---

# harsh-git-save

Land work on local `main`. No GitHub. No PRs.

Load shared rules: [../harsh-git/references/shared.md](../harsh-git/references/shared.md)

## Preconditions

- Must be inside a git repo. If not → tell the user to run `harsh-git` first; **stop**.

## Steps

1. Resolve current branch.

### On `main` (or repo trunk)

2. **Commit-all** (shared rules). Do **not** run `harsh-simplify-code`.
3. Done.

### On a feature branch

2. Ask whether to run `harsh-simplify-code` first. **Default: yes.**
   - If yes: read and follow `~/.cursor/skills/harsh-simplify-code/SKILL.md` fully. Wait until it finishes (ask loops, verification, agent guidance gate) before continuing.
   - If no: continue immediately.
3. **Commit-all** on the feature branch if the working tree is dirty.
4. Checkout `main` and merge the feature branch into `main` (no skip, no squash-unless-user-asks — default merge commit is fine).  
   - On real conflicts: stop, show conflicts, ask the user how to resolve. Do not invent resolutions silently.
5. Delete the feature branch (`git branch -d <name>`; use `-D` only if the user confirms after a refused `-d`).
6. Leave the user on `main`.

## Done when

- `main` contains the work
- Feature branch is deleted (feature path) or `main` has the new commit (main path)
