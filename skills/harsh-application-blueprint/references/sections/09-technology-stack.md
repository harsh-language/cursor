# Section 9: Technology Stack

**Key:** `technology_stack`

## Purpose

Defines the technologies used to implement the system.

## Questions

- What language is used?
- What framework is used?
- What storage solution is used?
- What third-party services are required?
- Why was each technology selected?
- **Version control:** Do you want a GitHub repo for this project? (`yes` / `no` / `existing` — already have a remote)

Ask the GitHub question in the same §9 pass as other stack choices (same turn if natural, or immediately after the last stack question). Record the answer in `repo_setup.github_remote` in `blueprint-status.yaml`. If `yes`, set `github_setup: pending` and run Phase 2a after §9 is `complete`. If `no`, set `github_setup: not_requested`. If `existing`, record URL in §9 and set `github_setup: complete`.

## Required output

- Technology inventory
- Selection rationale per major choice
- Dependency list
- Version control / remote hosting (GitHub yes/no/existing; URL if known)

## Minimum bar for `complete`

Every major layer (UI, logic, storage, build/deploy if known) has a choice and rationale. "Undecided" is allowed only with user-approved deferral in Outstanding Questions.

## Note

`ce-plan` may refine stack details after approval; blueprint must capture product-relevant constraints (platforms, offline, etc.).

After §9 completes with `github_remote: yes`, load [github-remote-setup.md](../github-remote-setup.md) before §10.
