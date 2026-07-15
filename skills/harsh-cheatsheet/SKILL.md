---
name: harsh-cheatsheet
description: >-
  Creates, updates, or combines personal cheat sheets in ~/.cursor/cheatsheets/
  using a fixed format (scope line, intent tables, 30-second guide, date footer).
  Use when the user asks for a cheat sheet, invokes harsh-cheatsheet, wants to
  create/update/refresh/combine a *cheatsheet*, or points at an existing
  ~/.cursor/cheatsheets/* file.
argument-hint: "[topic, path to existing sheet, or blank to inventory]"
disable-model-invocation: true
---

# harsh-cheatsheet

Maintain designer-friendly cheat sheets in `~/.cursor/cheatsheets/` only. Three workflows: **create-new**, **update-existing**, **combine-multiple**.

**Load references when needed:**

- Writing or aligning structure → [references/format.md](references/format.md)
- Researching a topic → [references/topic-sources.md](references/topic-sources.md)

Tone/structure examples (read; do not copy wholesale):

- `~/.cursor/cheatsheets/compound-engineering-skills.md` (legacy Compound sheet)
- `~/.cursor/cheatsheets/cursor-rules-agents-skills-cheatsheet.md`
- `~/.cursor/cheatsheets/git-github-cheatsheet.md`

## Hard rules

- Sheets only in `~/.cursor/cheatsheets/`. New files: `[topic]-cheatsheet.md`.
- Never overwrite or delete without explicit approve after suggestions.
- Suggestion tiers: **must-add** and **consider** only.
- Scope line filters what belongs on update/combine.
- **Last checked for update** on every stamped review; **Last updated** only when body changes.
- No index file. No hard page cap; shorten via suggestions.
- Offline research first; online per [topic-sources.md](references/topic-sources.md).

## Step 0: Inventory and route

1. Scan `~/.cursor/cheatsheets/` for `*cheatsheet*.md` and legacy `compound-engineering-skills.md`.
2. Resolve user topic/path against inventory.
3. Route:

| Situation | Workflow |
|-----------|----------|
| No overlap | **create-new** |
| Exact topic, path, or clear match | **update-existing** |
| Partial / related overlap | **Ask**: update · create-new · combine |

**Legacy Compound:** treat `compound-engineering-skills.md` as the Compound sheet — never duplicate it. On update, **consider** rename to `compound-engineering-cheatsheet.md` (must-add only if inventory confusion is likely).

If ambiguous, ask.

## Workflow 1 — create-new

1. Propose `~/.cursor/cheatsheets/[topic]-cheatsheet.md` + draft scope line.
2. **Ask before writing** (confirm name + scope).
3. Research (conversation + [topic-sources.md](references/topic-sources.md)).
4. Write per [format.md](references/format.md).
5. Set both footer dates to today (`YYYY-MM-DD`).

## Workflow 2 — update-existing

1. Read sheet; note scope line and dates.
2. Research offline + online per [topic-sources.md](references/topic-sources.md).
3. Suggestions only — no writes yet:
   - **Must-add:** missing in-scope facts, deprecations/renames, wrong/broken claims
   - **Consider:** shortening, polish, optional renames, nice-to-haves, scope tweaks
4. Shortening = proposed removals under those tiers (no silent deletes).
5. Changes found → present tiers → ask what to apply. Nothing found → “No changes. Update last-checked date?”
6. Approved body edits → apply; set both dates to today.
7. Check-stamp only → update **Last checked for update** only.

## Workflow 3 — combine-multiple

1. Confirm sources + one target `~/.cursor/cheatsheets/[topic]-cheatsheet.md`.
2. Merge → de-dupe → one scope line → must-add / consider (incl. orphan keep/delete).
3. **Ask before writing**.
4. On approve: write target; both dates today. Delete orphans only if user approved those consider items.

## Suggestion presentation

Before any write:

```markdown
### Must-add
- …

### Consider
- …

Apply: all must-add / selected items / none?
```

## After approved write

Confirm path, which dates changed, and any rename applied.

## Examples

**Create:** User says “cheat sheet for Figma MCP”. Inventory has no match → create-new → propose `~/.cursor/cheatsheets/figma-mcp-cheatsheet.md` + scope → ask → research → write with both dates.

**Update:** User says “update the git cheatsheet” or passes `~/.cursor/cheatsheets/git-github-cheatsheet.md` → update-existing → suggestions → ask → write only if approved.

**Overlap:** User says “skills cheat sheet”. Inventory hits Compound + rules/agents/skills sheets → ask update / create-new / combine before doing anything.
