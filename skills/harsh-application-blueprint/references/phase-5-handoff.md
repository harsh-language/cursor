# Phase 5: Approve and Implementation Plan Handoff

Load at Phase 5 only.

## Preconditions

- 18/18 sections `complete`
- `cross_check_pass: true`
- `completion_criteria_pass: true`
- `taxonomy_status: current` with `last_updated` set on `taxonomy.md`

## 5.1 Request approval

Ask the user to approve the blueprint. Summarize in 3–5 bullets: product name, MVP scope, key decisions, artifact paths.

If the user requests changes, return to Phase 2; do not set `phase: approved`.

## 5.2 On approval — update status

When the user approves, write to `blueprint-status.yaml`:

```yaml
phase: approved
approved_at: <ISO-8601 date>
```

## 5.3 Auto-plan gate

Read `auto_plan` from `blueprint-status.yaml`. Default **`true`** when the key is missing.

| `auto_plan` | Behavior |
|-------------|----------|
| `true` | Continue to §5.4 immediately — no extra prompt |
| `false` | Skip §5.4. Present §5.6 approve-only menu |

## 5.4 MVP scope from §18

Before invoking `ce-plan`, read **§18 Implementation Roadmap** in `docs/application-blueprint/blueprint.md`.

Extract the **first development phase** (Phase 1 / MVP / Stage 0 — whichever the blueprint names first):

- Phase name
- Deliverables
- "Done when" criteria

If §18 has only one phase or no explicit multi-phase breakdown, plan the full blueprint scope.

Compose the `ce-plan` skill argument:

```text
docs/application-blueprint/blueprint.md — Plan implementation for <first-phase-name> only (MVP / Phase 1 per §18 Implementation Roadmap). Scope boundary: <deliverables + done-when in one line>. Defer all later roadmap phases to future plans; cite them in an Out of Scope section.
```

Use repo-relative paths only.

## 5.5 Invoke ce-plan

Announce:

```text
Blueprint approved. Starting implementation plan for <first-phase-name>…
```

**Immediately invoke the `ce-plan` skill** in the current session with the argument from §5.4. Do not tell the user to type `/ce-plan` — fire the invocation now.

When `ce-plan` completes and has written a plan file under `docs/plans/`:

1. Record the path in `blueprint-status.yaml`:

   ```yaml
   implementation_plan_path: docs/plans/<filename>.md
   implementation_plan_scope: mvp  # or full when §18 has no later phases
   ```

2. Let `ce-plan` present its own post-generation menu (Start `/ce-work`, deeper doc review, create issue, Proof, done for now). **Do not replace or duplicate that menu.**

3. If the user selects **Start `/ce-work`**, invoke `ce-work` with the plan path — same as `ce-plan` handoff.

4. **Skill ends** after the user picks a terminal option (done for now, or `ce-work` completes its intake).

If `ce-plan` fails to produce a plan file, retry once with the same argument. If still missing, set `implementation_plan_path: null`, tell the user, and offer: retry `ce-plan` / done for now.

## 5.6 Approve-only menu (`auto_plan: false`)

```text
Blueprint approved.

Blueprint: <absolute path to blueprint.md>

auto_plan is false — implementation planning was not started.

What would you like to do next?
```

Options (numbered list; pick a number or describe what you want):

1. **Run implementation plan with `ce-plan` (Recommended)** — invoke `ce-plan` per §5.4–5.5 now
2. **Revise blueprint** — set `phase: in_progress`, return to Phase 2
3. **Done for now** — blueprint saved; run `/ce-plan docs/application-blueprint/blueprint.md` later

## Resume: `phase: approved`

| State | Action |
|-------|--------|
| `approved`, `implementation_plan_path` set | Point user to that plan; offer `/ce-work <path>` or re-run `ce-plan` to deepen |
| `approved`, no plan, `auto_plan: true` | Run §5.4–5.5 (interrupted prior session) |
| `approved`, no plan, `auto_plan: false` | Show §5.6 menu |

`ce-plan` before approval remains a **hard stop** during Phases 0–4.
