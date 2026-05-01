---
name: user-review
description: Seventh phase of the ccw workflow. Present a structured summary of all changes to the user for final review before opening a PR, then apply additional changes based on their feedback. Use this when the user invokes /ccw:user-review directly, or when the orchestrator (/ccw:start) delegates the user review phase.
---

# /ccw:user-review — Final User Review

Give the user a structured opportunity to review the change before it becomes externally visible (PR).

## Inputs
- The implementation with AI-review changes already applied
- `state.json.config.branch_name`
- Accumulated context in `state.json.notes`

## Procedure

1. **Summarize the change** for the user. Include:
   - **What was built** — in plain language, mapped back to the original design goal
   - **Files changed** — with a one-line summary per file
   - **Tests added** — what behaviors are covered
   - **Anything risky, unusual, or worth highlighting** — call out explicitly
   - **Trade-offs made along the way** — pull from `state.json.notes`
2. Show the user a `git diff --stat` (or similar) so they have a quick map of the change.
3. **Invite the user to review directly**:
   - "Take a moment to look through the diff. Anything you'd like to change before we open the PR?"
4. If the user has feedback:
   - Apply the requested changes
   - Re-run `config.test_command` to make sure nothing broke
   - Re-summarize the affected portion
5. Repeat steps 3–4 until the user explicitly approves moving to PR creation.

## Output
- Final code that the user has approved
- Any additional changes triggered by user feedback

## Exit condition
The user explicitly approves moving to PR creation.

## What to highlight in the summary
The summary should be more than a file list. Call out:
- Anything that touches a critical path
- Anything that changes a public API or contract
- Performance implications
- Migration steps that downstream code/users might need
- Anything you (Claude) felt uncertain about

## What NOT to do
- Don't push the user to approve quickly — this is their last chance to push back before PR.
- Don't open a PR yourself — that's `/ccw:pr`.
- Don't gloss over risky areas — surface them prominently in the summary.
