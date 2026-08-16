---
name: user-review
description: Fifth phase of the ccw workflow. Present a structured summary of all changes plus the AI review findings to the user for final review before opening a PR. Let the user decide reflect/skip/defer per finding and apply any additional changes from their feedback. Use this when the user invokes /ccw:user-review directly, or when the orchestrator (/ccw:start) delegates the user review phase.
---

# /ccw:user-review — Final User Review

Give the user a structured opportunity to review the change before it becomes externally visible (PR), and decide per-item what to do with the AI review findings.

## Inputs
- The implementation on the feature branch.
- `state.json.config.branch_name`.
- `state.json.artifacts.ai_review_findings` — structured findings produced by `ai-review`.
- Accumulated context in `state.json.notes`.

## Procedure

1. **Load AI review findings.** Read `state.json.artifacts.ai_review_findings`. If absent or empty, note that no AI review findings exist and continue with the change summary only.
2. **Summarize the change** for the user. Include:
   - **What was built** — in plain language, mapped back to the original design goal.
   - **Files changed** — with a one-line summary per file.
   - **Tests added** — what behaviors are covered.
   - **Anything risky, unusual, or worth highlighting** — call out explicitly.
   - **Trade-offs made along the way** — pull from `state.json.notes`.
3. **Present AI review findings** alongside the change summary, grouped by severity (`important`, `nit`, `info`). Show `id`, `summary`, and `detail` per finding.
4. Show the user a `git diff --stat` (or similar) so they have a quick map of the change.
5. **Decide per finding.** Ask the user, for each finding, to choose one of:
   - **reflect** — apply the fix now during this phase.
   - **skip** — intentionally ignore; record a one-line reason in `state.json.notes` as e.g. `"[User Review] finding <id> skipped: <reason>"`.
   - **defer** — leave for a follow-up; record as e.g. `"[User Review] finding <id> deferred: <reason>"`.
6. **Invite freeform feedback** on the diff itself:
   - "Anything else you'd like to change before we open the PR?"
7. **Apply changes** for items the user wants reflected, plus any freeform feedback:
   - Re-run the affected unit tests to make sure nothing broke.
   - If a fix is committed, append a one-line record to `state.json.notes` in the format `"[User Review] commit <short-hash>: <subject>"` (use `git log -1 --pretty='%h %s'`).
   - Re-summarize the affected portion.
8. Repeat steps 6–7 until the user explicitly approves moving to PR creation.

## Output
- Final code that the user has approved.
- Any additional changes triggered by user feedback or reflected AI review findings.
- `state.json.notes` updated with per-finding decisions and any commits made.

## Exit condition
The user explicitly approves moving to PR creation.

## What to highlight in the summary
The summary should be more than a file list. Call out:
- Anything that touches a critical path.
- Anything that changes a public API or contract.
- Performance implications.
- Migration steps that downstream code/users might need.
- Anything you (Claude) felt uncertain about.

## What NOT to do
- Don't push the user to approve quickly — this is their last chance to push back before PR.
- Don't open a PR yourself — that's `/ccw:pr`.
- Don't gloss over risky areas — surface them prominently in the summary.
- Don't silently drop AI review findings — every finding must end up reflected, skipped (with reason), or deferred (with reason).
