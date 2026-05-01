# Plan Phase

Skill: `/ccw:plan`

Decompose the implementation into well-defined, independently verifiable steps.

## Input

- The design document produced in the `document` phase

## Behavior

1. Decompose the implementation into meaningful steps. Each step should be independently verifiable.
2. For each step, specify:
   - Objective
   - Scope of changes
   - Verification method (unit test outline, etc.)
3. Ask the user to review and approve.
4. On first run, ask for verification commands (`test`, `lint`, etc.) and persist them to `config.json`.
5. Create a branch (decide `branch_name` and run `git checkout -b`).

## Output

- `plan.md` (in the chosen location, defaulting to `docs/features/<name>/plan.md`)
- `config.json` populated with verification commands
- A new git branch
- `state.json.artifacts.plan_doc` and `state.json.artifacts.implementation_steps` populated

## Exit Condition

- The user approves the plan.

## Notes

- The plan is a contract for the `implement` phase; bigger ambiguity here means more rework later.
- Steps should be small enough that each can land as a meaningful unit of progress.
