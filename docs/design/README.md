# Design — Index

This directory is the **source of truth** for the `ccw` plugin's design. The skills under `skills/` should always reflect what is documented here. When changing the plugin's behavior, update the relevant design file first, then update the corresponding `SKILL.md`.

## Overview

`ccw` is a Claude Code plugin that turns the recurring process of developing a single feature into a guided workflow. Instead of the user manually instructing each step, the skill drives the workflow phase-by-phase while the user only needs to respond and approve at each transition.

The workflow consists of 8 phases:

| Phase | Skill | Description |
|-------|-------|-------------|
| 1 | `/ccw:design` | Design the feature through dialogue with the user |
| 2 | `/ccw:document` | Write the agreed-upon design as a markdown document |
| 3 | `/ccw:plan` | Decompose the implementation into well-defined steps |
| 4 | `/ccw:implement` | For each step: [implement → review → unit test], iterating |
| 5 | `/ccw:verify` | Run integration tests and other checks against the full implementation |
| 6 | `/ccw:ai-review` | AI-driven code review (e.g., `/ultrareview`) |
| 7 | `/ccw:user-review` | The user personally reviews all changes |
| 8 | `/ccw:pr` | Create the PR, including any noteworthy development notes as PR comments |

The orchestrator skill `/ccw:start` walks the user through these phases in order.

## File Map

| File | Topic |
|------|-------|
| [01-decisions.md](01-decisions.md) | High-level design decisions and rationale |
| [02-architecture.md](02-architecture.md) | Skill structure, orchestrator vs sub-skills, invocation |
| [03-layout.md](03-layout.md) | Repository layout and runtime directory structure |
| [04-distribution.md](04-distribution.md) | How the plugin is published, installed, and updated |
| [05-state.md](05-state.md) | `state.json` schema and allowed values |
| [06-transitions.md](06-transitions.md) | Rules for moving between phases |
| [07-open-items.md](07-open-items.md) | Known gaps and future extensions |
| [phases/](phases/) | Per-phase specifications (one file per skill) |

## Per-Phase Specs

| Phase | Spec |
|-------|------|
| Orchestrator | [phases/start.md](phases/start.md) |
| Design | [phases/design.md](phases/design.md) |
| Document | [phases/document.md](phases/document.md) |
| Plan | [phases/plan.md](phases/plan.md) |
| Implement | [phases/implement.md](phases/implement.md) |
| Verify | [phases/verify.md](phases/verify.md) |
| AI Review | [phases/ai-review.md](phases/ai-review.md) |
| User Review | [phases/user-review.md](phases/user-review.md) |
| PR | [phases/pr.md](phases/pr.md) |

## How to navigate from a SKILL.md

Each `skills/<name>/SKILL.md` should reference its corresponding spec file in `docs/design/phases/<name>.md`. Reading either side gives a route to the other.
