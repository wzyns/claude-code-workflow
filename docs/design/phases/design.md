# Design Phase

Skill: `/ccw:design`

Conversational phase where the user and Claude clarify what the feature should do.

## Input

- User's feature idea (free-form dialogue)
- `state.json` (created by the orchestrator if not yet present)

## Behavior

Through conversation, clarify:

- Requirements
- Constraints
- Alternatives considered
- Trade-offs

Accumulate agreed points as the conversation progresses.

## Output

- Summary of the agreed design accumulated in `state.json.notes` (or as scratch notes referenced from there)

## Exit Condition

- The user signals "design complete" or otherwise approves moving on.

## Notes

- This phase produces a *summary*, not a finished document. The `document` phase turns it into a polished markdown file.
- Keep the conversation focused on *what* and *why*; defer *how* to the `plan` phase.
