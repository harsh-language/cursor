---
name: harsh-git-gud
description: >-
  Meta skill: inspect once, plan which harsh-git steps to run or skip, then
  init/branch/simplify/save/sync so local main is current and GitHub matches.
  Use when the user says harsh-git-gud, gud, full git save, or one-shot sync
  workflow.
disable-model-invocation: true
---

# harsh-git-gud

One-shot: get this project onto local `main` and mirrored to GitHub. Child skills do the real work; this skill **plans first**, then runs them in order.

## Child skills (read when executing that step)

| Skill | Path |
|-------|------|
| `harsh-git` | `~/.cursor/skills/harsh-git/SKILL.md` |
| `harsh-git-branch` | `~/.cursor/skills/harsh-git-branch/SKILL.md` |
| `harsh-git-save` | `~/.cursor/skills/harsh-git-save/SKILL.md` |
| `harsh-git-sync` | `~/.cursor/skills/harsh-git-sync/SKILL.md` |
| `harsh-simplify-code` | `~/.cursor/skills/harsh-simplify-code/SKILL.md` |

Shared rules: [../harsh-git/references/shared.md](../harsh-git/references/shared.md)

## Step 0 — Inspect once, then plan

Gather in one pass:

```bash
git rev-parse --is-inside-work-tree 2>/dev/null || echo "NO_GIT"
git branch --show-current 2>/dev/null
git status -sb 2>/dev/null
git remote -v 2>/dev/null
gh auth status 2>/dev/null
```

Present a short **execution plan** before any destructive step. Example shape:

```text
Plan:
1. [run|skip] harsh-git — …
2. [run|skip] harsh-git-branch — …
3. [run|skip] harsh-simplify-code — …
4. [run] harsh-git-save — …
5. [run] harsh-git-sync — …
```

Then execute. Label which step is running when asking questions so the user never loses track.

## Decision table

| Check | Action |
|-------|--------|
| No git repo | Run `harsh-git` |
| On `main` (or trunk) | Run `harsh-git-branch` (asks for name) |
| On a feature branch | **Skip** `harsh-git-branch`; run `harsh-simplify-code` to completion |
| After branch/simplify | Run `harsh-git-save` |
| After save (on `main`) | Run `harsh-git-sync` |

### Notes

- When on a feature branch, simplify runs **before** save (gud path). Save may still ask about simplify — if simplify just finished, skip asking again and continue save’s merge/delete steps.
- When the path started on `main`, `harsh-git-branch` creates the branch; then continue to save (save will ask about simplify on the feature branch).
- Sync asks whatever it needs (repo name, auth). Answer those when they appear; do not mutate GitHub before sync’s confirmations.
- Questions from child skills continue as needed. Do not batch past a child skill’s required ask.

## Done when

- Plan was shown first
- Applicable child skills finished in order
- Local `main` has the work and GitHub mirrors local (per `harsh-git-sync`)
