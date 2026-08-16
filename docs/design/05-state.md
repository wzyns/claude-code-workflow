# State Management (`state.json`)

The orchestrator and each sub-skill persist progress in `.claude/ccw/<feature-name>/state.json` within the consuming project. The schema below is the contract between phases.

## Schema

```json
{
  "feature_name": "user-auth",
  "started_at": "2026-05-01T10:00:00Z",
  "current_phase": "implement",
  "completed_phases": ["design", "plan"],
  "config": {
    "design_doc_path": ".claude/ccw/user-auth/design.md",
    "plan_doc_path": ".claude/ccw/user-auth/plan.md",
    "branch_name": "feature/user-auth"
  },
  "artifacts": {
    "design_doc": ".claude/ccw/user-auth/design.md",
    "plan_doc": ".claude/ccw/user-auth/plan.md",
    "implementation_steps": [
      { "step": 1, "description": "Define schema", "status": "done" },
      { "step": 2, "description": "API endpoints", "status": "in_progress" },
      { "step": 3, "description": "Frontend integration", "status": "pending" }
    ],
    "ai_review_findings": [
      { "id": "F1", "severity": "important", "summary": "Missing input validation in /signup", "detail": "The handler in api/signup.ts does not validate the email field; an empty string is accepted." },
      { "id": "F2", "severity": "nit", "summary": "Inconsistent log message casing", "detail": "Some log lines use lower-case, others Title Case." }
    ]
  },
  "notes": [
    "[Implement] commit abc1234: [#42] Add user schema",
    "[Implement] commit def5678: [#42] Add API endpoints"
  ]
}
```

## `notes` conventions

`notes` is a free-form append-only log used by the workflow. Two conventions are load-bearing:

- **Commit records.** Phases that create commits (`implement`, `user-review`) append `"[<Phase>] commit <short-hash>: <subject>"` immediately after each commit. `ai-review` reads these to know which commits belong to the workflow. `ai-review` itself does not create commits.
- **Decision/skip records.** Free-form notes describing design agreements, items intentionally skipped, etc. No format prescribed.

## `artifacts.ai_review_findings`

`ai-review` writes its self-review output here as a structured list. `user-review` reads this list and presents each finding alongside the diff summary so the user can decide per-item whether to reflect, skip, or defer.

Each finding has a minimal schema:

- `id` — short identifier within the run (e.g., `F1`, `F2`). Used to reference the finding in `notes` when the user skips or defers it.
- `severity` — one of `important`, `nit`, `info`. Higher severities should be presented first.
- `summary` — one-line headline of the issue.
- `detail` — longer explanation, including file/line references in free-form text when useful.

## Phase Values

`current_phase` and entries of `completed_phases` use these values:

- `design`, `plan`, `implement`, `ai-review`, `user-review`, `pr`, `done`

## `implementation_steps[].status` Values

- `pending`, `in_progress`, `done`
