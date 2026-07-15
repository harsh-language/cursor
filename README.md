# cursor-personal

Local source of truth for personal Cursor **skills** and **cheatsheets**.

## Layout

```
skills/          → linked as ~/.cursor/skills
cheatsheets/     → linked as ~/.cursor/cheatsheets
```

On this machine those `~/.cursor/` paths are symlinks into this repo. Edit files here (or via the symlink paths — same files).

## This machine (already set up)

```bash
~/.cursor/skills      → ~/Code/cursor-personal/skills
~/.cursor/cheatsheets → ~/Code/cursor-personal/cheatsheets
```

Commit after meaningful changes:

```bash
cd ~/Code/cursor-personal
git status
git add -A
git commit -m "Your message"
```

## Later: GitHub (not done yet)

When ready:

1. Create private repo `harsh-language/cursor-personal`
2. `git remote add origin git@github.com:harsh-language/cursor-personal.git`
3. `git push -u origin main`

## New machine (after GitHub exists)

```bash
git clone git@github.com:harsh-language/cursor-personal.git ~/Code/cursor-personal
ln -s ~/Code/cursor-personal/skills ~/.cursor/skills
ln -s ~/Code/cursor-personal/cheatsheets ~/.cursor/cheatsheets
```

Install Cursor and log in so marketplace plugins (Figma, Compound, etc.) restore separately.

## Scope

Tracked: `skills/`, `cheatsheets/`, this README.  
Not tracked: plugins, MCP secrets, project caches, or the rest of `~/.cursor/`.
