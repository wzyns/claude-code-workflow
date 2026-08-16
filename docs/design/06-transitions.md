# Phase Transition Rules

- Phase transitions are automatic. As soon as a sub-skill reports completion, the orchestrator advances `current_phase` and invokes the next sub-skill — there is no separate orchestrator-level confirmation prompt on top.
- Each sub-skill is responsible for collecting any user approval it needs *before* declaring itself complete. Examples: `design` waits for a "design complete" signal; `plan` iterates the plan content until the user approves; `implement` requires per-step approval; `user-review` requires explicit approval before completing. The orchestrator trusts the sub-skill's exit and does not re-ask.
- For visibility, the orchestrator prints a one-line announcement at each transition (e.g., `design complete — advancing to plan`).
- The user can still interrupt at any time. If the user asks to revisit a previous phase, the orchestrator rewinds `current_phase`, removes it from `completed_phases`, and re-invokes that sub-skill.
- The orchestrator (`/ccw:start`) is the canonical place that walks transitions; sub-skills can be invoked directly when re-running a single phase but should still update `state.json` consistently.
