# Artifact Gap Analysis

After Phase 0 harvest, before Phase 2. No section reference files.

**With artifacts:** run inside harvest sub-agent ([subagent-harvest.md](subagent-harvest.md)); parent receives summary only.

**Prompt only:** parent runs steps below inline.

Compare user artifacts (FigJam, Figma, plans, PDFs) to 18-section checklist. Write **implied_gaps** queue to yaml; summarize to user; then elicit.

## When

- Artifacts present → full analysis
- Prompt only → queue §1–2 gaps from text
- New artifact mid-session → re-run, merge, dedupe

Set `artifact_gap_analysis.completed: true` when queue written.

## Steps

1. Read yaml artifacts + blueprint headings/traceability (not full blueprint if large)
2. **Coverage map** per section key: `evidence` none|partial|strong, `missing` bullets
3. **UI → domain inference** (queue, don't invent):

| UI signal | Likely gap | § |
|-----------|------------|---|
| Slider/stepper | min, max, step, unit, storage | 5–6 |
| Toggle | default, scope, behaviour | 6 |
| Catalogue/picker | seed data, edit rules | 5–6 |
| Chart/calculated field | formula, empty state | 6, 17 |
| History/dates | one-per-X, backfill | 6, 14 |
| Timer | range, background | 6, 13 |
| Units | storage vs display | 6 |

4. **Reference data probe** — catalogues, standards PDFs, presets, formulas referenced in UI but not harvested → high priority
5. **Cross-artifact diff** — contradictions need user confirm; board-only screens → queue IA/flow
6. Write `implied_gaps`: `{ id, section, priority, source, signal, question, status: open }`
7. User summary: artifacts, N strong/partial/empty, top 3–5 gaps, first question preview

Phase 2 picks: high `implied_gaps` → dependency order → mark `answered` when resolved.

**Rules:** Infer ≠ invent. FigJam ≠ §10–13 without confirmed tokens/components.
