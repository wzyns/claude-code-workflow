# Directory and File Layout

## Plugin Repository Layout (this repo)

This repository is both the plugin source and the marketplace listing.

```
claude-code-workflow/
├── README.md
├── CLAUDE.md                       # Entry point for Claude (project conventions)
├── .gitignore
├── .claude-plugin/
│   ├── plugin.json                 # Plugin manifest (ccw, with explicit semver)
│   └── marketplace.json            # Marketplace listing pointing to this plugin
├── docs/
│   └── design/                     # Source of truth for plugin behavior
│       ├── README.md
│       ├── 01-decisions.md
│       ├── 02-architecture.md
│       ├── 03-layout.md            # This file
│       ├── 04-distribution.md
│       ├── 05-state.md
│       ├── 06-transitions.md
│       └── phases/
│           ├── start.md
│           ├── design.md
│           ├── plan.md
│           ├── implement.md
│           ├── ai-review.md
│           ├── user-review.md
│           └── pr.md
└── skills/
    ├── start/SKILL.md              # Orchestrator
    ├── design/SKILL.md
    ├── plan/SKILL.md
    ├── implement/SKILL.md
    ├── ai-review/SKILL.md
    ├── user-review/SKILL.md
    └── pr/SKILL.md
```

## Per-Feature Workflow Working Directory (in the consuming project)

Created by the plugin inside whatever project is using it. Metadata and artifacts always live under the same directory — the skills never ask for an alternative location:

```
.claude/ccw/<feature-name>/
├── state.json    # Progress state
├── design.md     # Design document (written at the end of the design phase)
└── plan.md       # Plan document (written by the plan phase)
```
