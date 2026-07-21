---
name: harsh-git-branch
description: >-
  Create a local experiment branch from main, or checkpoint-commit all work on
  the current feature branch. Asks for branch name only when on main. Use when
  the user says harsh-git-branch, new branch, start experiment branch, or
  checkpoint this branch.
disable-model-invocation: true
---

# harsh-git-branch

Local-only branching for solo work. No GitHub. No PRs.

Load shared rules: [../harsh-git/references/shared.md](../harsh-git/references/shared.md)

## Preconditions

- Must be inside a git repo. If not → tell the user to run `harsh-git` first; **stop**.

## Steps

1. Resolve current branch (`git branch --show-current`).

### On `main` (or repo trunk)

2. **Ask for the new branch name before any git action.** Do not invent a name.
3. After the user confirms the name:
   ```bash
   git checkout -b <name>
   ```
   Uncommitted work comes along the normal git way. **Do not commit.**

### Already on a feature branch

2. Do **not** ask for a branch name. Do **not** create another branch.
3. **Commit-all** on the current branch (shared rules). If clean, say nothing to commit.

## Done when

- User is on the new branch (from `main`), or current feature branch has a fresh commit-all (or was already clean)
