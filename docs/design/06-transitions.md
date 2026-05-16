# Phase Transition Rules

- The user must confirm most phase transitions. One transition auto-advances on success and does not prompt:
  - `ai-review → user-review` (ai-review only emits findings; the user makes per-finding decisions in `user-review`)
- At every other transition prompt, the skill explicitly invites the user to add input or request changes.
- The user can still interrupt an auto-advanced flow at any time (e.g., ask to stop, or request `back` from a later phase) — auto-advance only removes the routine confirmation prompt, not the ability to redirect.
- If the user requests rolling back to a previous phase, the orchestrator rewinds `current_phase` and resumes from that phase.
- The orchestrator (`/ccw:start`) is the canonical place that walks transitions; sub-skills can be invoked directly when re-running a single phase but should still update `state.json` consistently.
