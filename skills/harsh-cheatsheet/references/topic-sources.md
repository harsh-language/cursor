# Topic → research sources

Load when researching. Offline first; then online when the map requires it (or user asked for full refresh / sheet is stale).

## Sources map

| Topic type | Offline | Online |
|------------|---------|--------|
| Compound Engineering | Installed CE skills; existing `~/.cursor/cheatsheets/compound-engineering-skills.md` | `/ce-release-notes` or plugin release docs |
| Cursor (rules, agents, skills, product) | `~/.cursor/skills/`, user rules; `cursor-guide` | Cursor docs via cursor-guide / official docs |
| Git + GitHub CLI | `~/.cursor/cheatsheets/git-github-cheatsheet.md`; conversation `git`/`gh` usage | `gh` release notes only if stale or full refresh requested |
| Other / unknown | Conversation + files user cites | Web/docs only if the product changes over time |

## Classify the request

| Signals | Type |
|---------|------|
| Compound, CE, `ce-*`, lfg | Compound Engineering |
| Cursor, rules, AGENTS.md, skills placement | Cursor |
| git, gh, terminal PR, GitHub CLI | Git + GitHub CLI |
| Else | Other — ask before a heavy online pass |

## Research checklist

```
- [ ] Related sheet(s) in ~/.cursor/cheatsheets/
- [ ] Offline sources for type
- [ ] Online (Compound/Cursor always on update; others if stale or asked)
- [ ] Additions, renames, deprecations, removals
- [ ] Filter by scope line
- [ ] Split must-add vs consider
```

## Must-add vs consider

**Must-add:** in-scope omissions; renames/removals still listed as current; wrong commands; clear deprecations.

**Consider:** shortening; nice-to-haves; scope tweaks; polish; legacy rename; orphan deletion after combine.

Out of scope → do not must-add; only mention for create-new or combine.

## Stale heuristic

Favor online when last-checked is missing, user asked to update/refresh, or type is Compound/Cursor (always online on update).

Git: skip routine online unless stale/missing dates or full refresh requested.
