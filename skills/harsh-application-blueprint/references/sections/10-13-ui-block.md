# Sections 10–13: UI Block

**Keys:** `ui_specifications`, `component_inventory`, `navigation_structure`, `key_interactions`

**Hard gate:** Blueprint cannot complete until all four keys are `complete`.

Load this file once for the entire UI block. Mark each key complete only when its minimum bar is met.

---

## §10 UI Specifications (`ui_specifications`)

**Questions:** visual language, typography, color, spacing, accessibility standards

**Output:** design tokens, style guide summary, visual specs

**Minimum bar:** typography, color, spacing confirmed by user. Accessibility target stated. Figma/FigJam link insufficient until tokens extracted and confirmed.

---

## §11 Component Inventory (`component_inventory`)

**Questions:** components, states, configurable properties, dependencies

**Output:** catalog, variants, states, usage rules

**Minimum bar:** every screen maps to components; primary controls listed with states.

---

## §12 Navigation Structure (`navigation_structure`)

**Questions:** destinations, access paths, patterns (tabs/stack/drawer), deep links

**Output:** navigation map, routes, screen relationships

**Minimum bar:** every SCR* has an access path; default entry explicit.

---

## §13 Key Interactions (`key_interactions`)

**Questions:** critical interactions, feedback, transitions, system responses

**Output:** interaction specs, feedback behavior, motion

**Minimum bar:** core-loop interactions specify trigger, in-flight feedback, outcome, error.

---

## UI block elicitation mode

**Design artifact present (Figma/FigJam/Paper):** One pass — extract tokens, components, nav, interactions; present coverage report; ask user to confirm or fill gaps. Up to 3 coupled questions per turn within this block.

**No design artifact:** Walk §10 → §11 → §12 → §13 in order, one question per turn.

**Figma MCP:** Read variables/styles/components; never write tokens the user has not confirmed.
