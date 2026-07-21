---
name: harsh-git
description: >-
  Initialize local git tracking on the current folder for a solo designer
  workflow. If already a git repo, informs and stops. Use when the user says
  harsh-git, start git, init git, or wants git tracking without GitHub setup.
disable-model-invocation: true
---

# harsh-git

Turn the current folder into a local git repo. Does **not** create a GitHub remote.

Load shared rules: [references/shared.md](references/shared.md)

## Steps

1. Check whether this folder is already a git repo:
   ```bash
   git rev-parse --is-inside-work-tree
   ```
2. **If yes** → tell the user it is already tracked; **stop**. Do not re-init, commit, or touch remotes.
3. **If no**:
   - `git init`
   - Ensure the default branch is `main` (`git branch -M main` if needed)
   - Do **not** add a remote
   - If the folder has files (beyond `.git`), make one initial commit using shared commit-all rules with message `Initial commit`
   - If the folder is empty of trackable files, leave it initialized with no commit and say so

## Done when

- Repo exists on `main`, or user was told it already was a repo
- No `origin` was created by this skill
