# AI Review Phase

Skill: `/ccw:ai-review`

Get an automated AI review of the implementation and decide which feedback to apply.

## Input

- The completed implementation (preferably committed to the feature branch)

## Behavior

1. Ask the user how they would like to perform the AI review. No specific tool is prescribed — they may request a self-review by the assistant, delegate to a sub-agent, paste feedback from an external review tool, or skip the phase entirely. Proceed according to their answer.
2. When review feedback exists, summarize and organize it.
3. Decide together which items to address vs. ignore.
4. Apply code changes for items to address.

## Output

- Code changes addressing review feedback
- A summary of items addressed and items intentionally skipped (with reasons)

## Exit Condition

- The user confirms the AI review feedback has been handled.

## Notes

- AI review feedback is advisory, not mandatory. Trade-offs should be made explicit.
- The phase can be skipped — the user just signals skip when asked how they want to review.
