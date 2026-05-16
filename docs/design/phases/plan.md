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
3. Present the full plan content in chat for the user to review.
4. Iterate on feedback: revise the plan and re-present until the user explicitly approves.
5. After approval, ask for the storage location (default `.claude/ccw/<name>/plan.md`, or external such as Confluence) and save/publish the plan.
6. On first run, ask for verification commands (`test`, `lint`, etc.) and persist them to `config.json`.
7. Create a branch:
   - Infer the project's branch-name convention from `CLAUDE.md` and recent git history; fall back to `feature/<feature-name>`.
   - Confirm the name with the user, then run `git checkout -b`.

## Output

- `plan.md` (in the chosen location, defaulting to `.claude/ccw/<name>/plan.md`, or content posted to an external destination)
- `state.json.config.plan_doc_path` (or `plan_doc_external`) updated
- `config.json` populated with verification commands
- A new git branch
- `state.json.artifacts.plan_doc` and `state.json.artifacts.implementation_steps` populated

## Exit Condition

- The plan is approved by the user and saved (or published), verification commands are captured, and the branch is created.

## Notes

- If the user picks an external destination, attempt to publish directly using an available tool (MCP server, CLI, etc.). If no such tool is available, output the markdown body so the user can publish manually.
- The plan is a contract for the `implement` phase; bigger ambiguity here means more rework later — that is why content review happens here, before anything is persisted.
- Steps should be small enough that each can land as a meaningful unit of progress.
- The review loop ends only on explicit user approval. There is no need to surface the save path/link after persisting — the user already saw and approved the content.
