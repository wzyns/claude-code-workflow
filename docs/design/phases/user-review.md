# User Review Phase

Skill: `/ccw:user-review`

Give the user a structured opportunity to review the change before opening a PR.

## Input

- The implementation including AI-review changes

## Behavior

1. Present a summary of changes to the user (diff summary, scope of impact, etc.).
2. Give the user time to review directly.
3. Apply additional changes based on user feedback.

## Output

- Final code that the user is comfortable submitting

## Exit Condition

- The user approves moving to PR creation.

## Notes

- This is the user's last chance to push back before the change becomes externally visible (PR).
- The summary should call out anything risky, unusual, or worth highlighting — not just list files changed.
