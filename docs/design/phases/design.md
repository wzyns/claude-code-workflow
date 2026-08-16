# Design Phase

Skill: `/ccw:design`

Conversational phase where the user and Claude clarify what the feature should do, ending with the agreed design saved as a markdown document.

## Input

- User's feature idea (free-form dialogue)
- `state.json` (created by the orchestrator if not yet present)

## Behavior

1. Through conversation, clarify:
   - Requirements
   - Constraints
   - Alternatives considered
   - Trade-offs
2. Accumulate agreed points as the conversation progresses.
3. When the user signals the design is complete, compose the agreed points into a polished, self-contained markdown document and save it to `.claude/ccw/<name>/design.md`. Do not ask where to save it — the local default path is always used.
4. Report the saved file path to the user.

## Output

- Summary of the agreed design accumulated in `state.json.notes` (or as scratch notes referenced from there)
- `design.md` saved at `.claude/ccw/<name>/design.md`
- `state.json.config.design_doc_path` and `state.json.artifacts.design_doc` updated

## Exit Condition

- The user has signaled "design complete" (or otherwise approved moving on) and the design document has been saved.

## Notes

- Keep the conversation focused on *what* and *why*; defer *how* to the `plan` phase.
- The document should be self-contained enough to onboard a new reader to the feature.
- Do not ask the user to review the document content. Reporting the save path is the only user-facing acknowledgement. If the user wants to revise after the fact (whether the skill ran via the orchestrator or directly), they can re-invoke `/ccw:design`.
