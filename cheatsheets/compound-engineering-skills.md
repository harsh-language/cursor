# Compound Engineering — skill cheat sheet

Quick reference for Cursor. Invoke with `/skill-name` in chat.

**Typical product flow:** `ce-strategy` → `ce-ideate` / `ce-brainstorm` → `ce-doc-review` → `ce-plan` → `ce-work` → `ce-code-review` → ship

---

## Start here

| Skill | When to use |
|-------|-------------|
| **ce-setup** | First time in a project — check tools, config, missing deps |
| **ce-strategy** | New product or direction change — write/update `STRATEGY.md` |
| **ce-ideate** | Want options and ideas before picking a direction |
| **ce-brainstorm** | Vague idea — talk it through into a requirements doc |
| **ce-plan** | Ready to plan *how* to build (after brainstorm/spec exists) |
| **ce-work** | Execute a plan — actually build the thing |
| **lfg** | Hands-off full pipeline: plan → work → review → test → PR → fix CI |

---

## Think & decide (before building)

| Skill | When to use |
|-------|-------------|
| **ce-strategy** | Who it's for, what problem, metrics, tracks of work |
| **ce-ideate** | "What should we improve?" / "Surprise me with ideas" |
| **ce-brainstorm** | "Help me think through X" / scope is unclear |
| **ce-doc-review** | Review a spec or plan doc before you commit to it |
| **ce-proof** | Share a doc for human review in Proof (specs, plans, drafts) |
| **ce-slack-research** | "What did we decide in Slack about…?" |
| **ce-sessions** | "What did we try last time?" — past agent session history |

---

## Build & fix

| Skill | When to use |
|-------|-------------|
| **ce-work** | Implement from a plan doc |
| **ce-work-beta** | Same as ce-work, with experimental Codex delegation |
| **ce-debug** | Bug, error, test failure, stuck after a bad fix |
| **ce-frontend-design** | Build or improve UI with real design quality |
| **ce-polish** | Dev server + browser — iterate on UX together |
| **ce-simplify-code** | Clean up recent code without changing behavior |
| **ce-dhh-rails-style** | Ruby/Rails in 37signals / DHH style |
| **ce-agent-native-architecture** | Designing apps where agents are first-class |
| **ce-gemini-imagegen** | Generate or edit images via Gemini |

---

## Review & quality

| Skill | When to use |
|-------|-------------|
| **ce-code-review** | Review code before opening a PR |
| **ce-doc-review** | Stress-test a requirements or plan document |
| **ce-test-browser** | Browser-test pages changed on this branch |
| **ce-test-xcode** | Build & test iOS app on simulator |
| **ce-dogfood-beta** | Full QA pass on branch — test everything, auto-fix |
| **ce-agent-native-audit** | Score how agent-native your architecture is |

---

## Ship & git

| Skill | When to use |
|-------|-------------|
| **ce-commit** | Save work with a good commit message |
| **ce-commit-push-pr** | Commit, push, open PR (or just write PR description) |
| **ce-resolve-pr-feedback** | Address PR review comments |
| **ce-demo-reel** | GIF/screenshots for the PR — show what changed |
| **ce-promote** | Launch copy — tweet, changelog, email, announcement |
| **ce-worktree** | Isolate work in a separate git worktree |
| **ce-clean-gone-branches** | Delete stale local branches after merge |

---

## Learn & maintain

| Skill | When to use |
|-------|-------------|
| **ce-compound** | Document a solved problem for the team |
| **ce-compound-refresh** | Audit/clean stale docs in `docs/solutions/` |
| **ce-optimize** | Improve something measurable via experiments |
| **ce-product-pulse** | "How are we doing?" — usage/errors recap for a time window |
| **ce-riffrec-feedback-analysis** | Analyze a Riffrec screen+vocal feedback recording |

---

## Plugin meta

| Skill | When to use |
|-------|-------------|
| **ce-update** | Check if Compound Engineering plugin is up to date |
| **ce-release-notes** | What changed in recent plugin releases |
| **ce-report-bug** | Report a bug in the plugin itself |

---

## Beta / experimental

| Skill | When to use |
|-------|-------------|
| **ce-dogfood-beta** | Exhaustive branch QA with auto-fix |
| **ce-work-beta** | ce-work + external delegate mode |

---

## Pick the right skill (30-second guide)

| You want to… | Use |
|--------------|-----|
| Figure out *what* to build | `ce-brainstorm` (or `ce-ideate` for options first) |
| Figure out *how* to build it | `ce-plan` |
| Actually build it | `ce-work` |
| Fix something broken | `ce-debug` |
| Check code before PR | `ce-code-review` |
| Check a doc before building | `ce-doc-review` |
| Ship it | `ce-commit-push-pr` |
| Do everything automatically | `lfg` |

---

## Note on agents

Some skills (`ce-code-review`, `ce-doc-review`, `ce-plan`) spin up specialized **reviewer agents** behind the scenes — you don't invoke those directly. Use the skill above; it picks the right reviewers.

---

*Last updated: June 2026 · Plugin version in your install may add skills over time — run `/ce-release-notes` to check.*
