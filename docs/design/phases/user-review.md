# User Review Phase

Skill: `/ccw:user-review`

Give the user a structured opportunity to review the change before opening a PR, and decide what to do with the AI review findings.

## Input

- The implementation on the feature branch.
- `state.json.artifacts.ai_review_findings` — the structured findings produced by `ai-review`.
- Accumulated context in `state.json.notes`.

## Behavior

1. Present a summary of changes to the user (diff summary, scope of impact, etc.).
2. Present the AI review findings from `artifacts.ai_review_findings` alongside the change summary, grouped by severity. The user reviews both at the same time.
3. For each AI review finding, the user picks one of:
   - **reflect** — apply the fix now during this phase.
   - **skip** — intentionally ignore; record the reason in `state.json.notes`.
   - **defer** — leave for a follow-up; record the reason in `state.json.notes`.
4. Apply additional changes for items the user wants reflected, plus any extra feedback the user gives on the diff itself.

## Output

- Final code that the user is comfortable submitting.
- Any fix committed during user-review is appended to `state.json.notes` as `"[User Review] commit <short-hash>: <subject>"` (covering both the user's direct feedback and any reflected AI review findings).
- A short summary in `state.json.notes` of which AI review findings were reflected, skipped, or deferred.

## Exit Condition

- The user approves moving to PR creation.

## Notes

- This is the user's last chance to push back before the change becomes externally visible (PR).
- The summary should call out anything risky, unusual, or worth highlighting — not just list files changed.
- This phase is the single decision point for AI review findings; `ai-review` itself does not apply any fixes.
