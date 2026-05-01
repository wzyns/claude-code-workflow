# AI Review Phase

Skill: `/ccw:ai-review`

Get an automated AI review of the implementation and decide which feedback to apply.

## Input

- The completed implementation (preferably committed to the feature branch)

## Behavior

1. Ask the user to run `/ultrareview` (the skill cannot invoke `/ultrareview` itself).
2. When the user shares the results, summarize and organize them.
3. Decide together which items to address vs. ignore.
4. Apply code changes for items to address.

## Output

- Code changes addressing review feedback
- A summary of items addressed and items intentionally skipped (with reasons)

## Exit Condition

- The user confirms the AI review feedback has been handled.

## Notes

- AI review feedback is advisory, not mandatory. Trade-offs should be made explicit.
- If `/ultrareview` is unavailable in the user's environment, this phase can be skipped — but the user must explicitly opt out.
