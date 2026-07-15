# cursor

Personal kit for **Cursor on my machines**: custom skills and cheat sheets I use while working in projects.

**Last updated:** 2026-07-15

---

## Why this repo exists

Cursor keeps some things with your **account** (like installed plugins — Figma, Compound Engineering, etc.).

It does **not** sync the personal stuff that lives in a folder on your computer:

- **Skills** — reusable “how to do this job” playbooks you invoke in chat (e.g. `/harsh-cheatsheet`)
- **Cheat sheets** — short reference docs you keep open or point the agent at

Those live under `~/.cursor/` on whichever computer you’re on. A new laptop (or a cloud workspace) won’t magically get them when you log in.

This repo is the **source of truth** for that personal kit — everything I want available and up to date when I use Cursor locally.

---

## What’s in here

### Skills (`skills/`)

| Skill | What it’s for |
|-------|----------------|
| `harsh-cheatsheet` | Create / update / combine personal cheat sheets in a fixed format |
| `harsh-simplify-code` | Pre-PR cleanup; asks before risky UI/behavior changes |
| `harsh-application-blueprint` | Structured product/application blueprint workflow |

Invoke in chat with `/skill-name` (e.g. `/harsh-cheatsheet`).

### Cheat sheets (`cheatsheets/`)

| File | Topic |
|------|--------|
| `compound-engineering-skills.md` | Compound Engineering skills — when to use which |
| `cursor-rules-agents-skills-cheatsheet.md` | Rules vs AGENTS.md vs skills — what goes where |
| `git-github-cheatsheet.md` | Git + GitHub CLI (`git` / `gh`) in the terminal |

When you add or rename skills/sheets, update this README and the **Last updated** date.

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
