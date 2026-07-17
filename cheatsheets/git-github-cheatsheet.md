# Git + GitHub CLI — One-Page Reference

Terminal-only (`git` + `gh`). No browser needed.

---

## Concepts → meaning → commands

### Where work lives

| Concept | Plain meaning | Command(s) |
|--------|---------------|------------|
| **Repo** | Project + full history | `git clone <url>` · `gh repo view` |
| **Remote** | Shared copy on GitHub | `git remote -v` |
| **Origin** | Default remote name | `git push origin <branch>` |
| **Working tree** | Files you're editing now | `git status` |
| **Branch** | Separate line of work | `git branch` · `git switch -c <name>` |
| **Main** | Primary/shippable branch | `git switch main` |
| **HEAD** | "You are here" pointer | `git log -1` |
| **Fork** | Your copy of someone else's repo | `gh repo fork <owner/repo>` |
| **Tag** | Named release marker in history | `git tag` · `git tag v1.0` |
| **`.gitignore`** | Files Git should skip | edit file; check with `git status` |

### Status of your changes

| Concept | Plain meaning | Command(s) |
|--------|---------------|------------|
| **Untracked** | New files Git doesn't know | shows in `git status` |
| **Modified** | Changed but not saved in Git | `git diff` |
| **Staged** | Marked for next snapshot | `git add <file>` · `git diff --staged` |
| **Commit** | Saved snapshot (local) | `git commit -m "message"` · `git log -1` |
| **Stash** | WIP shelved for later | `git stash` · `git stash pop` |
| **Conflict** | Same lines edited twice | resolve → `git add` → `git commit` |
| **Draft PR** | PR marked WIP — not ready to merge | `gh pr create --draft` · `gh pr ready <num>` |

Local ladder: untracked → modified → staged → commit. Draft PR is the PR-side WIP status.

### What you do

| Concept | Plain meaning | Command(s) |
|--------|---------------|------------|
| **Commit** | Save a snapshot + message | `git commit -m "message"` |
| **Push** | Upload commits to GitHub | `git push` · `git push -u origin <branch>` |
| **Pull** | Download + merge remote changes | `git pull` |
| **Fetch** | Download only, don't merge | `git fetch` |
| **Merge** | Combine branches | `git merge <branch>` |
| **Rebase** | Replay your commits on top of another branch | `git rebase main` |
| **Diff** | See what changed | `git diff` · `git diff main..<branch>` |
| **Log** | See history of commits | `git log --oneline` |
| **Stash** | Temporarily shelve WIP | `git stash` · `git stash pop` |
| **Fork** | Make your own copy of a repo | `gh repo fork <owner/repo>` |
| **Review** | Approve or request changes on a PR | `gh pr review` |
| **PR** | Open / list / merge a pull request | `gh pr create` · `gh pr list` · `gh pr merge` |
| **Release** | Publish a version | `gh release create` |

### GitHub collaboration

| Concept | Plain meaning | Command(s) |
|--------|---------------|------------|
| **Issue** | Task/bug ticket | `gh issue list` · `gh issue create` |
| **PR** | "Please merge my branch" + review | `gh pr create` · `gh pr list` · `gh pr merge` |
| **Draft PR** | WIP — not ready to merge | `gh pr create --draft` |
| **Review** | Approve or request changes | `gh pr review` |
| **Checks / CI** | Automated tests on PR | `gh pr checks` |
| **Assignee / Reviewer** | Who owns / who must look | `gh pr edit --add-reviewer @user` |
| **Label** | Category tag on issue/PR | `gh pr edit --add-label design` |
| **Tag** | Named release marker | `git tag` · `git tag v1.0` |
| **Release** | Published version on GitHub | `gh release list` · `gh release create` |
| **Fork** | Your copy of someone else's repo | `gh repo fork <owner/repo>` |

---

## The loop

```
switch branch → edit → add → commit → push → gh pr create → gh pr review/merge
```

Stay current: `git pull` (on main) or `git fetch` + `git merge origin/main` (on your branch).

---

## Commands by intent

### Setup & auth

```bash
git clone <url>
gh auth login
gh auth status
```

### Where am I / what changed?

```bash
git status
git branch                          # local branches
git branch -a                       # all branches
git log --oneline -10
git diff                            # unstaged changes
git diff --staged                   # staged vs last commit
git diff main..HEAD                 # your branch vs main
git show <commit>                   # one commit's changes
```

### Save & share work

```bash
git add <file>
git add .
git commit -m "Clear outcome message"
git commit -am "message"            # tracked files only, skip separate add
git push
git push -u origin <branch>         # first push of new branch
```

### Branches

```bash
git switch main
git pull
git switch -c design/my-change      # create + switch
git switch <branch>                 # switch existing
git branch -d <branch>              # delete local (after merged)
git push origin --delete <branch>   # delete remote
```

### Sync with team

```bash
git fetch
git pull                            # fetch + merge current branch
git merge main                      # bring main into your branch
git rebase main                     # replay your commits on latest main
```

### Undo / fix (safe order)

```bash
git restore <file>                  # discard unstaged edits in file
git restore --staged <file>         # unstage
git commit --amend -m "new msg"     # fix last commit (not pushed)
git reset --soft HEAD~1             # undo last commit, keep changes
git stash                           # shelve WIP
git stash pop                       # restore shelved WIP
```

### Conflicts

```bash
# 1. open conflicted files, remove <<<<<<< ======= >>>>>>> markers
git add <file>
git commit                          # or git rebase --continue
git merge --abort                   # bail out of merge
git rebase --abort                  # bail out of rebase
```

### Issues (GitHub)

```bash
gh issue list
gh issue view <num>
gh issue create --title "..." --body "..."
gh issue close <num>
```

### Pull requests (GitHub)

```bash
gh pr list
gh pr list --author @me
gh pr view <num>
gh pr create --title "..." --body "..."
gh pr create --draft
gh pr checkout <num>                # check out PR branch locally
gh pr diff <num>
gh pr checks <num>
gh pr review <num> --approve
gh pr review <num> --request-changes --body "..."
gh pr comment <num> --body "..."
gh pr merge <num>                   # merge when approved
gh pr merge <num> --squash          # squash into one commit
gh pr close <num>
gh pr edit <num> --add-reviewer @user --add-label design
```

### Repo & releases

```bash
gh repo view
gh repo clone <owner/repo>
gh release list
gh release create v1.0 --notes "..."
```

---

## Naming & messages

- **Branches:** `design/…`, `fix/…`, `feature/…` — short, specific
- **Commits:** what changed for the user — `Fix mobile nav overlap`, not `Update file`
- **Link issues/PRs:** `#42` in commit or PR body

---

## Quick decisions

| Situation | Do this |
|-----------|---------|
| Starting new work | `git switch main && git pull && git switch -c design/thing` |
| Save progress | `git add . && git commit -m "..."` |
| Share for review | `git push -u origin design/thing && gh pr create` |
| Get latest main into your branch | `git fetch && git merge origin/main` |
| See if PR is safe to merge | `gh pr checks` + `gh pr view` |
| Merge approved work | `gh pr merge <num>` |
| Wrong file edited | `git restore <file>` |
| WIP but need to switch branch | `git stash` → switch → `git stash pop` |
| Behind remote | `git pull` before `git push` |
| Unsure / destructive | stop — ask before `reset --hard` or force push |

---

## Red flags

- **Never commit:** `.env`, keys, passwords (check `git status` first)
- **Don't push to `main` directly** — branch + PR
- **Don't `--force` push** unless someone tells you to
- **Conflict markers left in files** = broken merge — fix before commit

---

**TL;DR:** `git` = local snapshots & branches. `gh` = GitHub (issues, PRs, reviews, merge) from the terminal. Branch → commit → push → `gh pr create` → review → `gh pr merge`.
