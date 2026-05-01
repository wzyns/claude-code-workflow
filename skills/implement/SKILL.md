---
description: Fourth phase of the ccw workflow. Carry out the plan one step at a time, with implement → review → unit-test cycles per step. Updates state.json with step statuses. Use this when the user invokes /ccw:implement directly, or when the orchestrator (/ccw:start) delegates the implement phase.
---

# /ccw:implement — Step-by-step Implementation

Execute the plan one implementation step at a time. Each step is its own mini-cycle: implement → self-review → unit test.

## Inputs
- `state.json.artifacts.implementation_steps`
- `state.json.config.test_command`
- `state.json.config.lint_command` (optional)
- The plan document at `state.json.config.plan_doc_path`

## Procedure

For each `implementation_step` in order (where `status != "done"`):

1. **Mark the step as `in_progress`** in state.json. Update `updated_at`.
2. **Implement** the code for the step. Stay strictly within the scope of the step.
3. **Self-review** the change. Re-read the diff and check for:
   - Correctness (does it match the step's objective?)
   - Edge cases
   - Style consistency with surrounding code
   - Anything that should have a test but doesn't
4. **Write unit tests** for the new behavior.
5. **Run the test command** (`config.test_command`). Iterate until tests pass.
6. **Run the lint command** if configured. Fix any issues.
7. **Mark the step as `done`** in state.json.
8. **Report to the user**:
   - What was implemented
   - What tests cover it
   - Anything noteworthy
   - Confirm before moving to the next step

## Output
- Code changes
- Unit tests
- `state.json.artifacts.implementation_steps[].status` updated through the lifecycle

## Exit condition
All `implementation_steps` are `done`.

## When a step uncovers a planning issue
If a step reveals that the plan is wrong (e.g., a chosen approach doesn't work, a step needs to be split):
1. **Stop**.
2. Surface the issue to the user.
3. Suggest revising the plan (re-run `/ccw:plan`, or edit `plan.md` and `implementation_steps` in place).
4. Don't silently expand the step's scope.

## What NOT to do
- Don't skip the test/lint loop — even for "trivial" steps.
- Don't move to the next step without user confirmation.
- Don't refactor surrounding code unless it's required for the step.
- Don't batch multiple steps into one report — report per step so the user can intervene cleanly.
