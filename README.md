# cursor

Personal kit for **Cursor on my machines**: custom skills and cheat sheets I use while working in projects.

**Last updated:** 2026-07-27

---

## Why this repo exists

Cursor keeps some things with your **account** (like installed plugins — Figma, Compound Engineering, etc.).

It does **not** sync the personal stuff that lives in a folder on your computer:

- **Skills** — reusable “how to do this job” playbooks you invoke in chat (e.g. `/harsh-cheatsheet`)
- **Cheat sheets** — short reference docs you keep open or point the agent at
- **Rules** — always-on agent constraints (e.g. search verification)

Those live under `~/.cursor/` on whichever computer you’re on. A new laptop (or a cloud workspace) won’t magically get them when you log in.

This repo is the **source of truth** for that personal kit — everything I want available and up to date when I use Cursor locally.

---

## What’s in here

### Skills (`skills/`)

| Skill | What it’s for |
|-------|----------------|
| `harsh-figma-police` | Lock a Figma UI target, map diffs vs code, fix, and re-diff until parity |
| `harsh-cheatsheet` | Create, update, or combine personal cheat sheets in `~/.cursor/cheatsheets/` |
| `harsh-simplify-code` | Pre-PR cleanup; auto-fixes low-risk stuff, asks before UI/behavior-risk changes |
| `harsh-plan` | Stage 0 discovery into one blueprint+roadmap doc; per-stage `ce-plan` for builds |
| `harsh-product-design` | Product design and user-facing UI work; auto-picks modes; truth in `docs/product-design/` |
| `harsh-git` | Initialize local git tracking on the current folder (no GitHub) |
| `harsh-git-branch` | On `main`, create a named experiment branch; on a feature branch, checkpoint-commit all work |
| `harsh-git-main` | On `main`, commit-all; on a feature branch, optional simplify then merge into `main` and delete the branch |
| `harsh-git-sync` | Mirror local git to a private GitHub repo (refresh README, save dirty work, push all branches) |
| `ek-design-eng` | Emil Kowalski design-eng taste: UI polish, animation decisions, invisible details |
| `ek-review-animations` | Strict review of animation/motion code against Emil’s craft bar |
| `ek-improve-animations` | Audit motion in a codebase; prioritized, self-contained fix plans |
| `ek-find-animation-opportunities` | Find places that should (and shouldn’t) animate; propose recipes |
| `ek-animation-vocabulary` | Name a motion effect from a vague description (“what’s it called when…”) |
| `ek-apple-design` | Apple interface + fluid motion principles, translated for the web |
| `ek-pick-ui-library` | Pick a trusted library for a frontend task instead of hand-rolling |
| `writing-skills` | Write/refine skill files so they change agent behavior (process, why, strict, focused) |

Invoke in chat with `/skill-name` (e.g. `/harsh-figma-police`, `/harsh-cheatsheet`, `/harsh-git-sync`).

**Git workflow (solo):** local is home base; GitHub is a save file. Typical loop: `/harsh-git-branch` → work → `/harsh-git-main` → `/harsh-git-sync` (or just `/harsh-git-sync`, which refreshes README and saves dirty work first).

### Cheat sheets (`cheatsheets/`)

| File | Topic |
|------|--------|
| `compound-engineering-skills.md` | Compound Engineering skills — when to use which |
| `cursor-rules-agents-skills-cheatsheet.md` | Rules vs AGENTS.md vs skills — what goes where |
| `git-github-cheatsheet.md` | Git + GitHub CLI (`git` / `gh`) in the terminal |

### Rules (`rules/`)

| File | Topic |
|------|--------|
| `search-verification.mdc` | Never treat one empty search as proof of absence; dual-verify before claiming missing |

When you add or rename skills/sheets/rules, `/harsh-git-sync` refreshes this README automatically (and you can still edit it by hand anytime).

---

## How it works on this Mac

The real files live in this repo. Cursor reads them through shortcuts (symlinks):

```
~/.cursor/skills      →  this repo’s skills/
~/.cursor/cheatsheets →  this repo’s cheatsheets/
```

Edit either path — same files. Commit and push here when something changes.

---

## Install on a new machine

1. Install Cursor and **log in** (plugins from your account come back that way).
2. Clone this repo (pick a stable place; example uses `~/Code/cursor`):

```bash
git clone git@github.com:harsh-language/cursor.git ~/Code/cursor
```

3. Point Cursor at the repo folders with symlinks  
   (only if `~/.cursor/skills` and `~/.cursor/cheatsheets` don’t already exist as real folders):

```bash
ln -s ~/Code/cursor/skills ~/.cursor/skills
ln -s ~/Code/cursor/cheatsheets ~/.cursor/cheatsheets
```

4. Restart Cursor or open a new chat. You should see `/harsh-cheatsheet` and friends.

**If a symlink step fails** because a real folder already exists there: move or rename the old folder first, then run the `ln -s` commands again.

**Later updates on that machine:**

```bash
cd ~/Code/cursor
git pull
```

No need to copy files again — the symlinks already point here.

---

## What this does *not* include

- Marketplace **plugins** (install via Cursor; they follow your account)
- MCP secrets / `mcp.json`
- Project-only rules inside a single app repo
- The rest of random `~/.cursor/` cache and project junk

---

## Repo note

Private. For me. Keep the inventory tables above honest as the kit grows.
