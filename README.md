# cursor

Local source of truth for personal Cursor **skills** and **cheatsheets**.

## Layout

```
skills/          → linked as ~/.cursor/skills
cheatsheets/     → linked as ~/.cursor/cheatsheets
```

On this machine those `~/.cursor/` paths are symlinks into this repo. Edit files here (or via the symlink paths — same files).

## This machine

```bash
~/.cursor/skills      → ~/Code/cursor/skills
~/.cursor/cheatsheets → ~/Code/cursor/cheatsheets
```

Commit after meaningful changes:

```bash
cd ~/Code/cursor
git status
git add -A
git commit -m "Your message"
git push
```

## New machine

```bash
git clone git@github.com:harsh-language/cursor.git ~/Code/cursor
ln -s ~/Code/cursor/skills ~/.cursor/skills
ln -s ~/Code/cursor/cheatsheets ~/.cursor/cheatsheets
```

Install Cursor and log in so marketplace plugins (Figma, Compound, etc.) restore separately.

## Scope

Tracked: `skills/`, `cheatsheets/`, this README.  
Not tracked: plugins, MCP secrets, project caches, or the rest of `~/.cursor/`.
