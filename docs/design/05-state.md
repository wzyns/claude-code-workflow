# State Management (`state.json`)

The orchestrator and each sub-skill persist progress in `.claude/ccw/<feature-name>/state.json` within the consuming project. The schema below is the contract between phases.

## Schema

```json
{
  "feature_name": "user-auth",
  "started_at": "2026-05-01T10:00:00Z",
  "current_phase": "implement",
  "completed_phases": ["design", "document", "plan"],
  "config": {
    "design_doc_path": ".claude/ccw/user-auth/design.md",
    "design_doc_external": null,
    "plan_doc_path": ".claude/ccw/user-auth/plan.md",
    "test_command": "npm test",
    "lint_command": "npm run lint",
    "integration_test_command": "npm run test:integration",
    "branch_name": "feature/user-auth"
  },
  "artifacts": {
    "design_doc": ".claude/ccw/user-auth/design.md",
    "plan_doc": ".claude/ccw/user-auth/plan.md",
    "implementation_steps": [
      { "step": 1, "description": "Define schema", "status": "done" },
      { "step": 2, "description": "API endpoints", "status": "in_progress" },
      { "step": 3, "description": "Frontend integration", "status": "pending" }
    ]
  },
  "notes": []
}
```

## Phase Values

`current_phase` and entries of `completed_phases` use these values:

- `design`, `document`, `plan`, `implement`, `verify`, `ai-review`, `user-review`, `pr`, `done`

## `implementation_steps[].status` Values

- `pending`, `in_progress`, `done`

## Companion file: `config.json`

`config.json` holds settings that don't change frequently within a single feature (mostly verification commands). It is populated during the `plan` phase on first run and reused throughout.
