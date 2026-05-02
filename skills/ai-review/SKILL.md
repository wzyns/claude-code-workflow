---
name: ai-review
description: Sixth phase of the ccw workflow. Ask the user how they want to perform an AI review, obtain feedback (or accept skip), summarize the results, decide together which items to address, and apply changes. Use this when the user invokes /ccw:ai-review directly, or when the orchestrator (/ccw:start) delegates the AI review phase.
---

# /ccw:ai-review — AI Review

Get an automated AI review of the implementation and decide which feedback to apply.

## Inputs
- The completed implementation (preferably committed to the feature branch)

## Procedure

1. **Suggest committing** any uncommitted changes first, so the review covers a stable snapshot.
2. **Ask the user how to perform the review.** No specific tool is prescribed. The user may request a self-review by the assistant, delegate to a sub-agent, paste feedback from an external review tool, or skip the phase. Proceed according to their answer.
   - If the user chooses to skip, record the reason in `state.json.notes` (e.g., `"AI review skipped: <reason>"`) and treat the phase as complete.
3. When review feedback exists, **summarize** it:
   - Group by severity (critical, important, nit)
   - Highlight items with concrete code references (file:line)
4. **Discuss with the user** which items to address:
   - For each item, propose: "address now" / "skip" / "defer to follow-up"
   - For "skip" or "defer", record the reason in `state.json.notes`
5. **Apply changes** for items to address. After each fix:
   - Re-run `config.test_command` to make sure nothing broke
6. Summarize what was addressed and what was intentionally skipped.

## Output
- Code changes addressing review feedback
- Entries in `state.json.notes` describing items addressed and items intentionally skipped

## Exit condition
The user confirms the AI review feedback has been handled.

## What NOT to do
- Don't prescribe a specific review tool — ask the user how they want to review.
- Don't blindly apply all review feedback. Discuss trade-offs with the user.
- Don't treat AI review feedback as mandatory; it's advisory.
