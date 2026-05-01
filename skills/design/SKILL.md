---
name: design
description: First phase of the ccw workflow. Engage the user in a conversational design discussion about the feature — clarify requirements, constraints, alternatives, and trade-offs. Accumulate agreed points as notes in state.json. Use this when the user invokes /ccw:design directly, or when the orchestrator (/ccw:start) delegates the design phase.
---

# /ccw:design — Conversational Design

You are facilitating the **design** phase. The goal is to clarify *what* the feature should do and *why* — not how to build it.

## Inputs
- `.claude/ccw/<feature-name>/state.json` (must exist; created by orchestrator)
- The user's initial feature description (already in `state.json.notes` or shared in conversation)

## Procedure

1. Read state.json. Read existing notes for context.
2. Engage the user conversationally. For each topic, ask focused questions and accumulate agreed points:
   - **Requirements** — what behavior must the feature have?
   - **Constraints** — performance, compatibility, security, deadlines, team conventions
   - **Alternatives** — different approaches considered
   - **Trade-offs** — what's gained / lost by the chosen approach
3. After each substantive exchange, append the agreed point to `state.json.notes` as a short bullet.
4. Periodically summarize what's been agreed and what's still open.
5. Stay focused on *what* and *why*. Defer *how* (specific files, data structures, APIs) to the `plan` phase.

## Output
- `state.json.notes` accumulated with design agreements

## Exit condition
The user signals "design complete", "let's move on", or otherwise approves moving to the next phase.

## When invoked directly (without orchestrator)
If the user invokes `/ccw:design` directly to revise an earlier design, still read and update state.json. Report completion at the end. They can run `/ccw:start` to advance through subsequent phases.

## What NOT to do
- Don't write a polished design document — that's the `document` phase.
- Don't propose specific code, files, or APIs — that's the `plan` phase.
- Don't make decisions on the user's behalf — capture trade-offs and let the user choose.
