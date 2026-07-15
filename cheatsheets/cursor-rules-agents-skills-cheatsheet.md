# Cursor Rules / AGENTS / Skills — Cheat Sheet

Quick reference: what goes where, and how agents should fill UI when Figma is missing or partial.

---

## What is what

| Thing | What it is | Use when |
|-------|------------|----------|
| **Rules** | Project conventions the agent must follow | “Always / never do X in this codebase” |
| **AGENTS.md** | Project facts & pointers | “Here’s how this repo works” |
| **Skills** | Reusable workflows with steps | “Here’s how to do a multi-step job” |
| **Chat / instructions** | This task only | One-offs, current goal, temporary preference |

**Rule of thumb:**  
Repeated judgment call → **rule**.  
Stable project fact → **AGENTS.md**.  
Repeatable process → **skill**.  
Everything else → **say it in chat**.

---

## Rules vs AGENTS.md

Same job, different shape.

| | **Rules** (`.cursor/rules/*.mdc`) | **AGENTS.md** |
|--|----------------------------------|---------------|
| Form | Many small files | One overview doc |
| Scope | Always-on *or* file-specific (`globs`) | Always project context |
| Best for | Behavioral guardrails (“always / never”) | Facts & pointers (“how this repo works”) |

**Simple split:**  
`AGENTS.md` = **what is**.  
Rules = **what to do / not do**.

---

## Where they live

**In the repo (project):**
- Rules → `.cursor/rules/*.mdc`
- AGENTS → `AGENTS.md` at repo root
- Skills → usually `.cursor/skills/` (or team skills path)

**Outside the project (Cursor settings):**
- User rules → apply to *all* projects globally

For “guess in this app’s design language,” use **project rules**, not user rules.

---

## Scope ladder (same behavior type, different reach)

1. **User rules** — all projects  
2. **Always-apply project rules** — all work in this project  
3. **Scoped project rules** — only certain files/tasks  

---

## What should become a rule?

Ask: *“If I don’t write this down, will the agent reliably do the wrong thing again?”*

| Yes → rule | No → skip |
|------------|-----------|
| Repeated mistake across sessions | One-time request |
| Project-specific convention | Obvious from reading the code |
| Decision with tradeoffs the agent can’t infer | Volatile details |
| “Always do X, never do Y” | Long procedural workflows |

**Good rule material:** constraints with blast radius, non-obvious conventions, placement decisions, repeated anti-patterns.

**Don’t rule-ify:** task context, discoverable code details, long reference docs, sometimes-only preferences, constantly changing info.

Keep rules concise (~under 50 lines), one concern each. Prefer examples.

---

## Design system: don’t dump it into skills

**Half right.** Don’t turn the whole design system into skills/instructions.

| Put here | What |
|----------|------|
| **Rules** | Design language guardrails: tokens only, use existing components, fix at lowest layer, spacing/type/color conventions, don’t invent new primitives for one screen |
| **Skill** | One workflow: *build a new page (Figma optional)* — discover shells/components/tokens → compose → match patterns → don’t add one-offs |
| **Not docs** | Individual styles/components — the **code is the source of truth** |

Skills = process.  
Rules = judgment.  
Components/tokens already *are* the design language — duplicating them into instructions will rot and drift.

| Signal | Put it in… |
|--------|------------|
| “Always / never” | Rule |
| “Do these steps when building a page” | One skill |
| “What does Button look like?” | Read the component — don’t rewrite it in a skill |

---

## When Figma is missing or partial

**Principle:** Figma wins where it exists. Codebase wins for the rest. Agent invents *structure*, not *language*.

### Recommended setup

1. **Rule — design fills**  
   Incomplete/missing Figma → reuse existing screens/components/tokens; don’t invent new patterns; match nearest sibling screen; only invent layout composition, not new visuals.

2. **Skill — build UI (Figma optional)**  
   Check Figma if linked → map what exists → for gaps, find closest existing screen → compose from shared primitives → flag guesses.

3. **Optional rule — anti-slop**  
   No new colors, radii, card styles, type scales, or one-off components unless nothing close exists.

### Intended end state for UI work

1. Use Figma where available.  
2. Fill gaps by matching the app’s existing design language (screens, components, tokens, patterns).  
3. Do not invent a parallel visual system.  
4. Do not break shared primitives / cross-flow patterns.  
5. Flag what was guessed when Figma was missing.

---

## 30-second guide

| I want to… | Do this |
|------------|---------|
| Stop a repeated bad judgment | Write a **project rule** |
| Tell every agent how the repo works | Put it in **AGENTS.md** |
| Teach a repeatable multi-step job | Write a **skill** |
| Direct this one task | Say it in **chat** |
| Encode Button / spacing / tokens | **Don’t** — keep them in code |
| Make UI with no/partial Figma | Project **design-fill rule** + one **build-UI skill** |
| Apply a convention to every project | **User rule** (only if truly global) |
