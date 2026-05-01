---
name: implement
description: Fourth phase of the ccw workflow. Carry out the plan one step at a time. Each step is a mini-cycle: implement → unit test → user review → (commit on approval / revise on rejection). Updates state.json with step statuses. Use this when the user invokes /ccw:implement directly, or when the orchestrator (/ccw:start) delegates the implement phase.
---

# /ccw:implement — Step-by-step Implementation

Execute the plan one implementation step at a time. Each step is a mini-cycle that ends in a commit only when the user approves the diff.

## Inputs
- `state.json.artifacts.implementation_steps`
- `state.json.config.test_command`
- `state.json.config.lint_command` (optional)
- The plan document at `state.json.config.plan_doc_path`

## Per-step procedure

For each `implementation_step` in order (where `status != "done"`):

1. **Mark the step as `in_progress`** in state.json. Update `updated_at`.
2. **Implement** the code for the step. Stay strictly within the scope of the step.
3. **Verify**:
   - Write or update unit tests for the new behavior.
   - Run `config.test_command` and iterate until tests pass.
   - Run `config.lint_command` if configured. Fix any issues.
4. **Present for user review**:
   - Summarize what changed (files touched, behavior added).
   - Show or describe the diff at a level the user can react to.
   - Note any tests added and confirm they pass.
   - Explicitly ask: "Approve this step (commit), or request changes?"
5. **Branch on the user's response:**
   - **Approved** →
     - Create a commit containing only the changes for this step. Follow whatever commit-message conventions the consuming repo already uses (read recent `git log` to infer them); do not impose a format from this skill.
     - Mark the step as `done` in state.json.
     - Proceed to the next step (back to procedure 1 with the next step).
   - **Not approved** →
     - Capture the requested changes verbatim.
     - Return to procedure 2 (implement) for the same step. Do **not** commit. Keep the step at `in_progress`.
     - When the rework is ready, present for review again (procedure 4).

## Output
- One commit per approved step
- Code changes and unit tests on the feature branch
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
- Don't commit before the user approves the step.
- Don't skip the test/lint loop — even for "trivial" steps.
- Don't move to the next step without user approval of the current one.
- Don't refactor surrounding code unless it's required for the step.
- Don't batch multiple steps into one commit — one commit per approved step.
- Don't mark a step `done` while it's still in a rework loop; only after the commit is created.
