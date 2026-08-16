---
name: design
description: First phase of the ccw workflow. Engage the user in a conversational design discussion about the feature — clarify requirements, constraints, alternatives, and trade-offs. Accumulate agreed points as notes in state.json, then save the agreed design as a markdown document at .claude/ccw/<feature>/design.md before completing. Use this when the user invokes /ccw:design directly, or when the orchestrator (/ccw:start) delegates the design phase.
---

# /ccw:design — Conversational Design

You are facilitating the **design** phase. The goal is to clarify *what* the feature should do and *why* — not how to build it — and to persist the agreed design as a document before handing off to the `plan` phase.

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
6. When the user signals the design is complete ("design complete", "let's move on", or similar), compose the agreed points into a polished, self-contained markdown document. Suggested structure:
   - Title (feature name)
   - Goal
   - Requirements
   - Constraints
   - Approach (high level only — leave detailed steps for the `plan` phase)
   - Alternatives Considered
   - Trade-offs
   - Open Questions (if any)
7. Save the document to `.claude/ccw/<feature-name>/design.md`. **Do not ask where to save it** — the local default path is always used.
8. Report the saved file path to the user.
9. Update state.json:
   - `config.design_doc_path` = `.claude/ccw/<feature-name>/design.md`
   - `artifacts.design_doc` = same value

## Output
- `state.json.notes` accumulated with design agreements
- A markdown design document saved at `.claude/ccw/<feature-name>/design.md`
- `state.json.config.design_doc_path` and `state.json.artifacts.design_doc` updated

## Exit condition
The user has approved moving to the next phase and the design document has been saved.

## When invoked directly (without orchestrator)
If the user invokes `/ccw:design` directly to revise an earlier design, still read and update state.json, and re-save the design document at the end. Report completion when done. They can run `/ccw:start` to advance through subsequent phases.

## What NOT to do
- Don't include implementation steps in the document — that's the `plan` phase.
- Don't propose specific code, files, or APIs — that's the `plan` phase.
- Don't make decisions on the user's behalf — capture trade-offs and let the user choose.
- Don't ask where to save the document, and don't ask the user to review or approve the document content. Reporting the save path is the only acknowledgement needed — if the user wants to revise after the fact, they can re-invoke `/ccw:design`.
