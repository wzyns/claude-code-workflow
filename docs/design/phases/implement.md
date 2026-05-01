# Implement Phase

Skill: `/ccw:implement`

Carry out the plan one step at a time, with built-in review and unit testing per step.

## Input

- `plan.md` and `state.json.artifacts.implementation_steps`

## Behavior

For each `implementation_step` in order:

1. Implement the code for the step.
2. Self-review the step's output.
3. Write and run unit tests.
4. Report the step's result to the user and confirm progression to the next step.

Update `state.json.artifacts.implementation_steps[].status` as steps move from `pending` → `in_progress` → `done`.

## Output

- Code changes
- Unit tests
- Updated `state.json` with step statuses

## Exit Condition

- All `implementation_steps` are `done`.

## Notes

- Each step is its own mini-cycle: implement → review → test. This catches regressions early.
- If a step uncovers an issue with the plan, prefer pausing to revise the plan over silently expanding scope.
