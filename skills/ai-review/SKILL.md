---
name: ai-review
description: Sixth phase of the ccw workflow. Get an AI review by asking the user to run /ultrareview, then summarize the results, decide together which items to address, and apply changes. Use this when the user invokes /ccw:ai-review directly, or when the orchestrator (/ccw:start) delegates the AI review phase.
---

# /ccw:ai-review — AI Review

Get an automated AI review of the implementation and decide which feedback to apply.

## Inputs
- The completed implementation (preferably committed to the feature branch)

## Procedure

1. **Suggest committing** any uncommitted changes first, so the review covers a stable snapshot.
2. **Ask the user to run `/ultrareview`** in this same session. You cannot invoke `/ultrareview` yourself — it must be user-triggered.
3. When the user shares the review results, **summarize** them:
   - Group by severity (critical, important, nit)
   - Highlight items with concrete code references (file:line)
4. **Discuss with the user** which items to address:
   - For each item, propose: "address now" / "skip" / "defer to follow-up"
   - For "skip" or "defer", record the reason in `state.json.notes`
5. **Apply changes** for items to address. After each fix:
   - Re-run `config.test_command` to make sure nothing broke
6. Summarize what was addressed and what was intentionally skipped.
7. Update state.json `updated_at`.

## Output
- Code changes addressing review feedback
- Entries in `state.json.notes` describing items addressed and items intentionally skipped

## Exit condition
The user confirms the AI review feedback has been handled.

## If /ultrareview is unavailable
If the user reports `/ultrareview` is not available in their environment:
1. Ask if they want to skip this phase or use an alternative review tool.
2. If skipped, record explicitly in `state.json.notes` (e.g., `"AI review skipped: <reason>"`).
3. Move on with explicit user opt-out — do not silently bypass.

## What NOT to do
- Don't try to invoke `/ultrareview` yourself — instruct the user.
- Don't blindly apply all review feedback. Discuss trade-offs with the user.
- Don't treat AI review feedback as mandatory; it's advisory.
