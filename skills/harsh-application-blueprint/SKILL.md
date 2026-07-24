---
name: harsh-application-blueprint
description: Elicit a full Application Blueprint (18 sections + taxonomy.md); optional MVP plan on approval.
disable-model-invocation: true
---

# Harsh Application Blueprint

Creates the **product blueprint** (WHAT to build). On approval, **auto-runs `ce-plan`** for the first roadmap milestone (MVP / Phase 1 per §18) unless `auto_plan: false`. No `ce-plan` / `ce-work` during elicitation (Phases 0–4).

Elicit from **user answers or confirmed artifacts**. Do not invent product, UI, or scope.

## Plan outputs (project repo)

- `docs/application-blueprint/blueprint.md` — full spec (agents)
- `docs/application-blueprint/blueprint-status.yaml` — progress (agents)
- `taxonomy.md` — **repo root, humans only**
- `docs/plans/*.md` — implementation plan (agents), created by `ce-plan` after approval

Load [artifacts.md](references/artifacts.md) at Phase 0. Load [taxonomy-template.md](references/taxonomy-template.md) at first taxonomy write.

## Rules

1. One question per turn (UI block: up to 3 if same design pass).
2. Progress: `Plan: N/18 · taxonomy: [draft|current] · next: [section]`
3. User owns decisions. Harvest → don't re-ask. No application code while this skill runs.
4. Load **one** section reference per loop turn (max 2). Never all 18 at once.
5. **Harvest + gap analysis** — dispatch sub-agent when artifacts exist; parent never ingests large artifact bodies. [subagent-harvest.md](references/subagent-harvest.md)
6. **Repo setup exception** — Local git + optional GitHub remote setup are allowed after §9 when the user opted in. No app code, dependencies, or scaffolding. [github-remote-setup.md](references/github-remote-setup.md)

## Phases

**0 Initialize** — Create artifacts + `taxonomy.md` if missing. If no project repo exists, bootstrap via `create_project` MCP (or `git init`) and `move_agent_to_root`; set `repo_setup.local_git: complete` in yaml. **If artifacts exist:** dispatch sub-agent (harvest + 0b per [subagent-harvest.md](references/subagent-harvest.md)); parent reviews summary, confirms contradictions, then Phase 2. **If prompt only:** parent harvests from prompt inline. [section-index.md](references/section-index.md) for order.

**0b Gap analysis** — Included in sub-agent pass when artifacts exist. If prompt-only, parent runs [artifact-gap-analysis.md](references/artifact-gap-analysis.md) inline.

**1 Discovery** (if §1–4 gaps) — One Compound Engineering skill/turn: [skill-invocation-matrix.md](references/skill-invocation-matrix.md). User confirms before merge.

**2 Elicitation** — Read yaml → high-priority gap or earliest incomplete section → one section ref → ask → write blueprint + distill taxonomy → repeat.

**2a GitHub setup** — When §9 is `complete` and `repo_setup.github_remote` is `yes` with `github_setup: pending`, run [github-remote-setup.md](references/github-remote-setup.md) before continuing to §10. Ask GitHub preference during §9 elicitation (see `sections/09-technology-stack.md`).

UI: `ui_block_mode: design_artifact` when Figma/FigJam/Paper linked. First write: [quality-bar.md](references/quality-bar.md) once.

**2b Cross-check** — [cross-check.md](references/cross-check.md). Fix via Phase 2; sync taxonomy.

**3 Stress-test** — `ce-brainstorm` review-only (gap stress-test). Update blueprint/taxonomy if needed.

**4 Validate** — [completion-criteria.md](references/completion-criteria.md) + `ce-doc-review` on blueprint.

**5 Approve + implementation plan** — 18/18, criteria pass, taxonomy complete (`status: current`, `last_updated` set). Ask approval → `phase: approved` → [phase-5-handoff.md](references/phase-5-handoff.md): auto-invoke `ce-plan` on blueprint (MVP scope from §18) when `auto_plan` is true → record `implementation_plan_path` → `ce-plan` post-generation menu (start build / done for now). **Skill ends** at terminal handoff choice.

Set `auto_plan: false` in `blueprint-status.yaml` before approval to skip auto-planning and get approve-only handoff.

`ce-plan` may later fill taxonomy "Find in code" rows. Taxonomy not auto-synced — refresh per taxonomy.md maintenance note.

## Resume

`in_progress` → read `blueprint-status.yaml`; if `artifact_gap_analysis.completed` false and artifacts exist, re-dispatch harvest sub-agent; else if `repo_setup.github_setup` is `pending` and `github_remote` is `yes`, Phase 2a; else Phase 2.

`approved` → [phase-5-handoff.md](references/phase-5-handoff.md) resume table.

## Hard stops

Incomplete taxonomy at root · incomplete §10–13 · `ce-plan` before approval (Phases 0–4) · >2 section refs/turn · UI complete from link alone · parent pastes large artifact bodies into chat (write via sub-agent or files only)
