# Phase 5: Stage 0 complete → roadmap + per-stage ce-plan

Load at Phase 5 only.

## Preconditions

- 18/18 sections `complete`
- `cross_check_pass: true`
- `completion_criteria_pass: true`
- `taxonomy_status: current` with `last_updated` set on `taxonomy.md`

## 5.1 Request Stage 0 approval

Ask the user to approve Stage 0 (the meta blueprint). Summarize in 3–5 bullets: product name, MVP scope, key decisions, meta doc path.

If the user requests changes, return to Phase 2; do not set `phase: approved`.

## 5.2 On approval — update status

When the user approves, write to `blueprint-status.yaml`:

```yaml
phase: approved
approved_at: <ISO-8601 date>
stage0_status: complete
```

## 5.3 Map Stage 1+ into the same meta doc

Do **not** run a whole-app `ce-plan`. Do **not** create a separate whole-app implementation plan.

1. Propose Stage 1, Stage 2, … (ordered build stages) based on the approved meta doc.
2. Each stage: name, deliverables, done-when, and the **specific design frame/section links** that apply (from the meta doc).
3. Ask the user to confirm or reorder.
4. Write the confirmed stages into **§18 Implementation Roadmap** of `docs/application-blueprint/blueprint.md` (same file — one meta document).
5. Update yaml:

```yaml
roadmap_mapped: true
next_build_stage: <first stage id, e.g. stage_1>
stage_plans: {}  # stage_id → docs/plans/<file>.md as each ce-plan completes
```

## 5.4 Start a build stage → ce-plan for that stage only

When the user asks to start any stage **after Stage 0** (or picks “start Stage N”):

1. Read that stage’s scope + design links from the meta doc §18 (and linked SCR*/F* rows).
2. Compose the `ce-plan` argument:

```text
docs/application-blueprint/blueprint.md — Plan implementation for <stage-name> only. Scope boundary: <deliverables + done-when in one line>. Design references for this stage: <frame/section links>. Defer all other roadmap stages to future plans; cite them in Out of Scope.
```

3. Announce: `Starting ce-plan for <stage-name>…`
4. **Immediately invoke `ce-plan`** with that argument. Do not tell the user to type `/ce-plan`.
5. When `ce-plan` writes `docs/plans/<file>.md`, record:

```yaml
stage_plans:
  <stage_id>: docs/plans/<filename>.md
current_stage_plan_path: docs/plans/<filename>.md
```

6. Let `ce-plan` present its own post-generation menu. Do not replace it.
7. If the user selects **Start `/ce-work`**, invoke `ce-work` with that stage plan path.
8. **Skill ends** after a terminal option (done for now, or `ce-work` intake completes).

If `ce-plan` fails to produce a plan file, retry once. If still missing, tell the user and offer: retry `ce-plan` / done for now.

## 5.5 Design link maintenance (during later stages)

If designs change after a stage is built:

- Prefer **what is built** over stale Stage N design links.
- Update or remove old frame/section links in the meta doc when the user confirms.
- Add new links for updated designs when provided.

## 5.6 Done-for-now menu (roadmap mapped, no stage started)

```text
Stage 0 approved. Roadmap is in the meta doc.

Meta doc: <absolute path to blueprint.md>

What would you like to do next?
```

Options:

1. **Start Stage &lt;N&gt; with `ce-plan`** (Recommended) — §5.4 for that stage
2. **Revise meta doc** — set `phase: in_progress`, return to Phase 2
3. **Done for now** — resume later; starting any post–Stage 0 stage runs `ce-plan` for that stage

## Resume: `phase: approved`

| State | Action |
|-------|--------|
| `approved`, `roadmap_mapped` false | Run §5.3 |
| `approved`, roadmap mapped, user starts Stage N | Run §5.4 for Stage N |
| `approved`, `current_stage_plan_path` set | Point to that plan; offer `/ce-work` or re-run `ce-plan` for the same stage |
| `approved`, done for now | Show §5.6 |

`ce-plan` during Stage 0 (Phases 0–4) remains a **hard stop**. Whole-app `ce-plan` is never used.
