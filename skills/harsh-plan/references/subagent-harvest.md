# Sub-Agent: Harvest + Gap Analysis

Load at Phase 0 when user supplied artifacts (plans, FigJam, Figma, PDFs, pasted docs). **Parent does not full-read large artifacts inline.**

## When to dispatch

| Condition | Action |
|-----------|--------|
| User linked or attached harvestable artifacts | Dispatch **one** sub-agent for harvest + 0b gap analysis |
| Prompt only, no artifacts | **Skip** sub-agent — parent runs prompt-only gap queue inline |
| New artifact mid-session | Re-dispatch harvest+gap sub-agent; parent merges |

Use `Task` with `subagent_type: generalPurpose` (needs file write). Do **not** use `explore` — it cannot write plan files.

## Parent stays responsible for

- Creating empty artifacts if missing (before dispatch)
- Presenting sub-agent summary to user
- Confirming contradictions and harvest mapping
- All Phase 2+ questions and approval

## Sub-agent prompt (include verbatim structure)

Give the sub-agent:

1. Paths: artifact URLs/paths, `docs/application-blueprint/blueprint.md`, `blueprint-status.yaml`, `taxonomy.md`
2. Instruction: read [artifact-gap-analysis.md](artifact-gap-analysis.md) steps (this file's sibling — use full path in prompt)
3. Instruction: read [artifacts.md](artifacts.md) for yaml schema and [taxonomy-template.md](taxonomy-template.md) for taxonomy shape
4. **Harvest** — map artifact content into blueprint sections + distill taxonomy; set section statuses in yaml; record sources in `artifacts:`
5. **Gap analysis** — coverage map, UI→domain inference, `implied_gaps` queue; set `artifact_gap_analysis.completed: true`
6. **Do not** invent product/UI/rules — mark `partial` or queue gaps instead
7. **Do not** ask the user questions

## Sub-agent return (short — parent context only)

Sub-agent ends with a compact report, not raw artifact dumps:

```
HARVEST: N sections strong / N partial / N empty; contradictions: [list or none]
TAXONOMY: draft updated yes/no
TOP_GAPS: 3–5 bullets (id + one-line question)
FIRST_ELICIT: suggested next_section + one-line question preview
NEEDS_USER_CONFIRM: contradictions or low-confidence mappings only
```

Parent writes nothing the sub-agent already wrote unless fixing errors.

## Fallback

If `Task` is unavailable or sub-agent fails: parent harvests inline per Phase 0/0b in SKILL.md — still avoid pasting full artifact content into chat; write to files first, summarize in ≤15 lines.

## Token rule

Parent chat receives **only** the return block above. Never relay FigJam/Figma/PDF body text through the parent thread.
