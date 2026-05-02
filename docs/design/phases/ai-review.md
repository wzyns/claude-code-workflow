# AI Review Phase

Skill: `/ccw:ai-review`

Get an AI review of the implementation and decide which feedback to apply.

## Input

- The completed implementation (preferably committed to the feature branch)

## Behavior

1. Ask the user only **proceed or skip**.
   - **proceed**: the assistant self-reviews the commits already recorded in `state.json.notes` for this workflow. The review subject is the union of those commits' diffs; no external tool is invoked.
   - **skip**: record the reason in `state.json.notes` and treat the phase as complete.
2. Summarize and organize the self-review feedback.
3. Decide together which items to address vs. ignore.
4. Apply code changes for items to address. Fix commits made during this run are not re-reviewed in the same run.

## Output

- Code changes addressing review feedback
- A summary of items addressed and items intentionally skipped (with reasons)
- Any fix committed during ai-review is appended to `state.json.notes` as `"[AI Review] commit <short-hash>: <subject>"`. Commits created in this run are not re-reviewed in the same run.

## Exit Condition

- The user confirms the phase is complete (feedback addressed, or skipped).

## Notes

- AI review feedback is advisory, not mandatory. Trade-offs should be made explicit.
- The phase can be skipped — the user just answers "skip" at the prompt.
