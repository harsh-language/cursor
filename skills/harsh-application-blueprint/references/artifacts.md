# Plan Artifacts

Load once at Phase 0.

## Paths

| Path | Role |
|------|------|
| `docs/application-blueprint/blueprint.md` | 18-section spec |
| `docs/application-blueprint/blueprint-status.yaml` | Agent tracker |
| `taxonomy.md` | Human summary at repo root (only human doc at root) |

## blueprint-status.yaml (minimal)

```yaml
phase: in_progress
product_name: ""
completion_pct: 0
taxonomy_status: not_started  # draft | current
auto_plan: true  # false = approve blueprint only; no auto ce-plan
implementation_plan_path: null
implementation_plan_scope: null  # mvp | full
next_section: product_overview
ui_block_mode: null
sections:  # each: not_started | partial | blocked | complete
  product_overview: not_started
  feature_requirements: not_started
  user_flows: not_started
  information_architecture: not_started
  data_model: not_started
  business_rules: not_started
  state_management: not_started
  architecture_overview: not_started
  technology_stack: not_started
  ui_specifications: not_started
  component_inventory: not_started
  navigation_structure: not_started
  key_interactions: not_started
  edge_cases: not_started
  security_privacy: not_started
  analytics_measurement: not_started
  testing_strategy: not_started
  implementation_roadmap: not_started
gaps: {}
artifacts: {}
artifact_gap_analysis: { completed: false, implied_gaps: [] }
repo_setup:
  github_remote: null  # yes | no | existing
  local_git: pending   # pending | complete | skipped
  github_setup: pending  # pending | complete | skipped | not_requested
  github_url: null
cross_check_pass: false
completion_criteria_pass: false
approved_at: null
```

Full blueprint headings: [blueprint-template.md](blueprint-template.md).

## Taxonomy distill

§1 → What this is · §3 → Flows · §4+§12 → Screens · §2,5,6 → Key concepts · Find in code after build
