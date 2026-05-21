# Implement Phase

Skill: `/ccw:implement`

Carry out the plan one step at a time. Each step is its own mini-cycle ending in a commit.

## Input

- `plan.md` and `state.json.artifacts.implementation_steps`

## Behavior

For each `implementation_step` in order (where `status != "done"`):

1. **Implement** the code for the step.
2. **Test** by writing or updating unit tests for the new behavior and running them. Iterate until they pass.
3. **User review** — present the diff and a brief summary of the change to the user.
4. **Branch on the user's response:**
   - **Approved →** create a commit for this step, mark the step as `done`, move to the next step.
   - **Not approved →** capture the requested changes and return to step 1 (still on the same `implementation_step`). Do not commit.

Update `state.json.artifacts.implementation_steps[].status` as steps move from `pending` → `in_progress` → `done`. The step stays `in_progress` across any rework loop and only flips to `done` after the commit is created.

## Output

- One commit per approved step
- Code changes and unit tests
- Updated `state.json` with step statuses
- Each commit appended to `state.json.notes` as `"[Implement] commit <short-hash>: <subject>"` so later phases (notably `ai-review`) can locate the changes that belong to this workflow

## Exit Condition

- All `implementation_steps` are `done`.

## Notes

- The per-step commit is intentional: it gives the user a clean checkpoint to revert to and makes the eventual PR easier to review step-by-step.
- If a step uncovers an issue with the plan, prefer pausing to revise the plan over silently expanding scope.
- Don't squash multiple in-progress steps into a single commit; one commit per approved step preserves the structure of the plan in git history.
