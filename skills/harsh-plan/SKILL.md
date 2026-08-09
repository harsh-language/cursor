---
name: harsh-plan
description: Stage 0 product discovery into one meta blueprint+roadmap doc; then per-stage ce-plan builds.
disable-model-invocation: true
---

# harsh-plan

Creates **one meta document**: product blueprint + implementation roadmap together. Skill Phases 0–4 are **Stage 0** (discovery / Q&A) inside that document. After Stage 0, propose Stage 1+ order for user confirmation. **Every stage after Stage 0 starts with `ce-plan` for that stage only.** No whole-app `ce-plan`. No `ce-plan` / `ce-work` during Stage 0 (Phases 0–4).

Elicit from **user answers or confirmed artifacts**. Do not invent product, UI, or scope.

## Plan outputs (project repo)

- `docs/application-blueprint/blueprint.md` — **one meta doc**: 18-section blueprint + roadmap/stages (agents)
- `docs/application-blueprint/blueprint-status.yaml` — progress (agents)
- `docs/plans/*.md` — **per-stage** implementation plans from `ce-plan` when starting Stage 1+

Load [artifacts.md](references/artifacts.md) at Phase 0.

## Rules

1. One question per turn (UI block: up to 3 if same design pass).
2. Progress: `Plan: N/18 · next: [section]`
3. User owns decisions. Harvest → don't re-ask. No application code while this skill runs Stage 0.
4. Load **one** section reference per loop turn (max 2). Never all 18 at once.
5. **Harvest + gap analysis** — dispatch sub-agent when artifacts exist; parent never ingests large artifact bodies. [subagent-harvest.md](references/subagent-harvest.md)
6. **Repo setup exception** — Local git + optional GitHub remote setup are allowed after §9 when the user opted in. No app code, dependencies, or scaffolding. [github-remote-setup.md](references/github-remote-setup.md)
7. **Design references** — During Q&A, when a screen/feature needs designed UI, ask for a **specific Figma/FigJam/Paper frame or section link** (not only a file-level link). Store it beside that screen/feature in the meta doc. Broad file links alone are not enough.
8. **Built work wins** — If designs change after a stage is built, update or remove stale design links so the meta doc matches what is being built. Prefer current build over older stage designs.

## Phases (Stage 0 = Phases 0–4)

**0 Initialize** — Create artifacts if missing. If no project repo exists, bootstrap via `create_project` MCP (or `git init`) and `move_agent_to_root`; set `repo_setup.local_git: complete` in yaml. **If artifacts exist:** dispatch sub-agent (harvest + 0b per [subagent-harvest.md](references/subagent-harvest.md)); parent reviews summary, confirms contradictions, then Phase 2. **If prompt only:** parent harvests from prompt inline. [section-index.md](references/section-index.md) for order.

**0b Gap analysis** — Included in sub-agent pass when artifacts exist. If prompt-only, parent runs [artifact-gap-analysis.md](references/artifact-gap-analysis.md) inline.

**1 Discovery** (if §1–4 gaps) — One Compound Engineering skill/turn: [skill-invocation-matrix.md](references/skill-invocation-matrix.md). User confirms before merge.

**2 Elicitation** — Read yaml → high-priority gap or earliest incomplete section → one section ref → ask → write meta doc → repeat. Capture specific design frame/section links when UI is involved (Rule 7).

**2a GitHub setup** — When §9 is `complete` and `repo_setup.github_remote` is `yes` with `github_setup: pending`, run [github-remote-setup.md](references/github-remote-setup.md) before continuing to §10. Ask GitHub preference during §9 elicitation (see `sections/09-technology-stack.md`).

UI: `ui_block_mode: design_artifact` when Figma/FigJam/Paper linked. First write: [quality-bar.md](references/quality-bar.md) once.

**2b Cross-check** — [cross-check.md](references/cross-check.md). Fix via Phase 2.

**3 Stress-test** — `ce-brainstorm` review-only (gap stress-test). Update meta doc if needed.

**4 Validate** — [completion-criteria.md](references/completion-criteria.md) + `ce-doc-review` on meta doc.

**5 Stage 0 complete → roadmap + stage builds** — 18/18, criteria pass. Ask approval of Stage 0 → `phase: approved` → [phase-5-handoff.md](references/phase-5-handoff.md): propose Stage 1+ in §18 → user confirms order → write stages into the **same** meta doc. When the user starts any stage after Stage 0, **run `ce-plan` for that stage only** (input = meta doc + that stage’s scope and design links). Record the stage plan path in yaml. **Skill ends** at terminal handoff choice.

## Resume

`in_progress` → read `blueprint-status.yaml`; if `artifact_gap_analysis.completed` false and artifacts exist, re-dispatch harvest sub-agent; else if `repo_setup.github_setup` is `pending` and `github_remote` is `yes`, Phase 2a; else Phase 2.

`approved` → [phase-5-handoff.md](references/phase-5-handoff.md) resume table (map stages if missing; on “start Stage N” → `ce-plan` for Stage N).

## Hard stops

Incomplete §10–13 · `ce-plan` during Stage 0 (Phases 0–4) · whole-app `ce-plan` · >2 section refs/turn · UI complete from file-level link alone · parent pastes large artifact bodies into chat (write via sub-agent or files only)
