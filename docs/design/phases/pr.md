# PR Phase

Skill: `/ccw:pr`

Create the pull request and capture relevant development notes as PR comments.

## Input

- Final code on the feature branch

## Behavior

1. Follow the standard Claude Code PR protocol:
   - Analyze changes; draft title and body
   - On user approval, run `git push` and `gh pr create`
2. Add any notable development notes as PR comments:
   - Trade-offs made during implementation
   - Known limitations
   - Follow-up tasks deferred from this PR
   - Anything reviewers should pay extra attention to

## Output

- A live PR URL
- PR comments documenting development notes
- `state.json.current_phase = "done"`

## Exit Condition

- PR is created and the workflow is marked complete.

## Notes

- The PR comments are where the workflow's accumulated context (notes from prior phases, AI-review trade-offs, etc.) finally meets the human reviewer. Treat this step seriously.
