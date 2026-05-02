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
3. Ask for the storage location (default `.claude/ccw/<name>/plan.md`, or external such as Confluence).
4. Ask the user to review and approve.
5. On first run, ask for verification commands (`test`, `lint`, etc.) and persist them to `config.json`.
6. Create a branch:
   - Infer the project's branch-name convention from `CLAUDE.md` and recent git history; fall back to `feature/<feature-name>`.
   - Confirm the name with the user, then run `git checkout -b`.

## Output

- `plan.md` (in the chosen location, defaulting to `.claude/ccw/<name>/plan.md`, or content posted to an external destination)
- `state.json.config.plan_doc_path` (or `plan_doc_external`) updated
- `config.json` populated with verification commands
- A new git branch
- `state.json.artifacts.plan_doc` and `state.json.artifacts.implementation_steps` populated

## Exit Condition

- The user approves the plan.

## Notes

- If the user picks an external destination, attempt to publish directly using an available tool (MCP server, CLI, etc.). If no such tool is available, output the markdown body so the user can publish manually.
- The plan is a contract for the `implement` phase; bigger ambiguity here means more rework later.
- Steps should be small enough that each can land as a meaningful unit of progress.
