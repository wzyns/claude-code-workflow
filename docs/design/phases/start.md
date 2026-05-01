# Orchestrator (`start`)

Skill: `/ccw:start`

The orchestrator is the canonical entry point for the workflow. It walks the user through the 8 phases in order, confirming each transition.

## Input

- The user's invocation of `/ccw:start`
- Optionally an existing `.claude/workflow/<name>/state.json` (resume case)

## Behavior

1. Discover any in-progress workflows under `.claude/workflow/`.
2. If one or more exist → ask the user whether to resume one or start a new one.
3. If starting new → ask for:
   - Feature name (used as the workflow directory name)
   - High-level idea (one or two sentences for context)
4. Initialize `state.json` with `current_phase = "design"` and an empty `completed_phases`.
5. Loop:
   - Determine the current phase from `state.json`.
   - Invoke the corresponding sub-skill via the `Skill` tool.
   - When the sub-skill reports completion, update `state.json` (move the phase into `completed_phases`, advance `current_phase`).
   - Ask the user to confirm progressing to the next phase, inviting any input or change requests.
6. When `current_phase` reaches `done`, summarize the completed workflow and exit.

## Output

- An updated `state.json` reflecting progress
- Calls into sub-skills, which produce their own outputs (documents, code, PR)

## Exit Condition

- `state.json.current_phase == "done"` after the `pr` phase succeeds
- Or the user explicitly aborts the workflow

## Notes

- The orchestrator never performs phase-specific work itself; all real work is delegated to sub-skills.
- Sub-skills can also be invoked directly (e.g., `/ccw:plan`) without the orchestrator. In that case, the sub-skill is responsible for keeping `state.json` consistent.
- Phase transitions are governed by [06-transitions.md](../06-transitions.md).
