# Phase Transition Rules

- The user must confirm every phase transition.
- At each transition prompt, the skill explicitly invites the user to add input or request changes.
- If the user requests rolling back to a previous phase, the orchestrator rewinds `current_phase` and resumes from that phase.
- The orchestrator (`/ccw:start`) is the canonical place that walks transitions; sub-skills can be invoked directly when re-running a single phase but should still update `state.json` consistently.
