# Cheat sheet format

Load when creating or aligning a sheet. Prefer aligning existing sheets to this shape without inventing filler sections.

## Template

```markdown
# [Topic] — Cheat Sheet

[Scope line: 1–2 sentences. What this covers. What it leaves out.]

---

## What is what

| Thing | Plain meaning | When to use |
|-------|---------------|-------------|
| … | … | … |

---

## [Intent group 1]

| … | … |
|---|---|

---

## 30-second guide

| I want to… | Do this |
|------------|---------|
| … | … |

---

## Red flags

- …

---

**TL;DR:** [one line]

---

Last checked for update: YYYY-MM-DD
Last updated: YYYY-MM-DD
```

## Section rules

| Section | Required? | Notes |
|---------|-----------|--------|
| Title | Yes | `— Cheat Sheet`, or `— One-Page Reference` if command-heavy |
| Scope line | Yes | Boundary contract; directly under title |
| What is what | When useful | Mental model before long lists |
| Main reference | Yes | By **intent**, not alphabet. Tables preferred; code blocks only for loops |
| 30-second guide | Yes | I want to… → Do this |
| Red flags | When relevant | Naming, anti-patterns |
| TL;DR | Preferred | One-line close |
| Footer dates | Yes | Exact labels in template |

Omit unused optional sections. Separate major blocks with `---`.

## Scope line

Boundary contract — not a TOC:

1. What the sheet **covers**
2. What it **leaves out** (or implies via a tight include)

On update/combine: in-scope → must-add/consider; out-of-scope → other sheet or combine, do not stuff in.

Examples:

- Compound: CE skills in Cursor; not Team Kit / Superpowers / raw git
- Git: terminal `git` + `gh`; not GitHub UI or Cursor PR skills
- Rules/Agents/Skills: what goes where; not full skill-authoring tutorials

## Tone

Scannable, plain language. Short cells. No tutorial prose in tables. Prefer “when to use.”

## Footer dates

```markdown
---

Last checked for update: YYYY-MM-DD
Last updated: YYYY-MM-DD
```

| Field | When |
|-------|------|
| Last checked for update | Every stamped review (incl. no content change) |
| Last updated | Body content changes only |

Create/combine write → both today. Body update → both today. Check-stamp only → checked date only.

Missing footer on old sheets → add on first approved update write.

## Filename

New: `~/.cursor/cheatsheets/[topic]-cheatsheet.md`

Legacy: `~/.cursor/cheatsheets/compound-engineering-skills.md` = Compound sheet; **consider** rename to `compound-engineering-cheatsheet.md` on update.
