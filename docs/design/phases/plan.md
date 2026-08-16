# Plan Phase

Skill: `/ccw:plan`

Decompose the implementation into well-defined, independently verifiable steps.

## Input

- The design document produced in the `design` phase

## Behavior

1. Decompose the implementation into meaningful steps. Each step should be independently verifiable.
2. For each step, specify:
   - Objective
   - Scope of changes
   - Verification method (unit test outline, etc.)
3. Present an abbreviated summary in chat for the user to review — list each step's heading and objective only; do not dump per-step scope, affected files, or verification details into chat. Mention that the full plan with those details will be saved to the document. If the user asks about a specific step, expand only that step.
4. Iterate on feedback: revise the plan and re-present until the user explicitly approves.
5. After approval, save the plan to `.claude/ccw/<name>/plan.md`. Do not ask where to save it — the local default path is always used.
6. Create a branch:
   - Infer the project's branch-name convention from `CLAUDE.md` and recent git history; fall back to `feature/<feature-name>`.
   - Confirm the name with the user, then run `git checkout -b`.

## Output

- `plan.md` saved at `.claude/ccw/<name>/plan.md`
- `state.json.config.plan_doc_path` updated
- A new git branch
- `state.json.artifacts.plan_doc` and `state.json.artifacts.implementation_steps` populated

## Exit Condition

- The plan is approved by the user and saved, and the branch is created.

## Notes

- The plan is a contract for the `implement` phase; bigger ambiguity here means more rework later — that is why content review happens here, before anything is persisted.
- Steps should be small enough that each can land as a meaningful unit of progress.
- The review loop ends only on explicit user approval. There is no need to surface the save path after persisting — the user already saw and approved the content.
