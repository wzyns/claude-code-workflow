# Orchestrator (`start`)

Skill: `/ccw:start`

The orchestrator is the canonical entry point for the workflow. It walks the user through the 7 phases in order, confirming each transition.

## Input

- The user's invocation of `/ccw:start`
- Optionally an existing `.claude/ccw/<name>/state.json` (resume case)

## Behavior

1. Discover existing workflows by reading each `.claude/ccw/<name>/state.json`. Classify every entry into one of three buckets:
   - **in-progress** — parses cleanly and `current_phase != "done"`
   - **done** — parses cleanly and `current_phase == "done"`
   - **broken** — `state.json` is missing, unreadable, unparseable, or lacks a usable `current_phase`
2. Decide what to surface:
   - **Done** workflows are silently excluded; never mention them.
   - **Broken** entries are reported on a single separate warning line (e.g., `Warning: skipped N broken workflow state(s): <paths>`), but never offered for resume.
   - If one or more **in-progress** workflows exist → ask the user whether to resume one or start a new one.
   - If zero in-progress workflows remain, proceed straight to the new-feature flow without prompting.
3. If starting new → ask for:
   - Feature name (used as the workflow directory name)
   - High-level idea (one or two sentences for context)
4. Initialize `state.json` with `current_phase = "design"` and an empty `completed_phases`.
5. Loop:
   - Determine the current phase from `state.json`.
   - Invoke the corresponding sub-skill via the `Skill` tool.
   - When the sub-skill reports completion, update `state.json` (move the phase into `completed_phases`, advance `current_phase`).
   - **Confirmation policy**:
     - For `ai-review → user-review`, advance automatically without prompting the user. `ai-review` is designed to have nothing for the user to decide at the transition boundary — it only emits findings, and decisions happen in `user-review`.
     - For all other transitions, ask the user to confirm progressing to the next phase, inviting any input or change requests.
   - The user can still interrupt an auto-advanced flow at any time, or request `back` from a later phase to revisit an auto-advanced one — auto-advance only removes the routine confirmation prompt.
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
