# Meta doc template (blueprint + roadmap)

Copy to `docs/application-blueprint/blueprint.md` on first run. This is the **one** planning document — Stage 0 discovery and Stage 1+ roadmap live here.

```markdown
---
title: Application Blueprint
product:
status: draft
created:
last_updated:
approved:
design_references: []  # file-level ok as index; prefer per-screen/frame links in §4 / §11 / §18
---

# Application Blueprint: [Product Name]

> One meta doc: product spec + implementation roadmap. Stage 0 = discovery; Stage 1+ confirmed after approval; each later stage starts with ce-plan.

## 1. Product Overview

## Traceability

| ID | Type | Name | Links |
|----|------|------|-------|
| F1 | Feature | | FL1, SCR1 |

## 2. Feature Requirements

<!-- F* IDs for MVP features; future/non-goals listed separately -->

## 3. User Flows

<!-- FL* IDs; mermaid; happy + failure paths -->

## 4. Information Architecture

<!-- SCR* IDs; sitemap; hierarchy; per-screen design frame/section link when known -->

## 5. Data Model

<!-- E* IDs; entities, attributes, relationships -->

## 6. Business Rules

<!-- BR* IDs; validation, calculation, constraints -->

## 7. State Management

## 8. Architecture Overview

## 9. Technology Stack

<!-- Language, framework, storage, third-party services, version control (GitHub URL if set) -->

## 10. UI Specifications

## 11. Component Inventory

## 12. Navigation Structure

## 13. Key Interactions

## 14. Edge Cases & Failure States

## 15. Security & Privacy

## 16. Analytics & Measurement

## 17. Testing Strategy

<!-- T* IDs linked to BR* and FL* -->

## 18. Implementation Roadmap

<!-- Stage 0 = this discovery. After approval: Stage 1+ with deliverables, done-when, design links -->

## Outstanding Questions

## Approval Record
```
