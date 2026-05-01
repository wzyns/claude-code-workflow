# Open Items and Future Extensions

These are intentional gaps in the current design. They may be addressed in future versions.

- Concurrent multi-feature support (currently assumes one in-progress feature at a time)
- Schema versioning for `state.json` (migration strategy when the schema evolves)
- Direct integration with external systems such as Confluence (currently the user publishes manually)
- Workflow restart and rollback commands (e.g., `/ccw:start --reset`, `/ccw:start --back`)
- Optional retrospective phase after a feature is shipped
