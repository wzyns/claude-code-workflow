# CLAUDE.md

This file orients Claude Code when working on this repository.

## What this project is

This repo is the source of the **`ccw` Claude Code plugin** (claude-code-workflow). The plugin provides a guided, phase-by-phase workflow for developing a single feature: design → document → plan → implement → verify → AI review → user review → PR.

The plugin consists of:

- An orchestrator skill (`/ccw:start`) that walks the user through all 8 phases
- 8 per-phase sub-skills (`/ccw:design`, `/ccw:document`, ...) that can also be invoked independently

## Repository layout

```
.
├── CLAUDE.md                 # This file
├── README.md                 # Human-facing project description and install instructions
├── .claude-plugin/
│   ├── plugin.json           # Plugin manifest (name=ccw, explicit semver)
│   └── marketplace.json      # Marketplace listing for this single-plugin repo
├── docs/
│   └── design/               # Source of truth — see docs/design/README.md
│       ├── README.md
│       ├── 01-decisions.md
│       ├── 02-architecture.md
│       ├── 03-layout.md
│       ├── 04-distribution.md
│       ├── 05-state.md
│       ├── 06-transitions.md
│       ├── 07-open-items.md
│       └── phases/
│           ├── start.md
│           ├── design.md
│           ├── document.md
│           ├── plan.md
│           ├── implement.md
│           ├── verify.md
│           ├── ai-review.md
│           ├── user-review.md
│           └── pr.md
└── skills/
    ├── start/SKILL.md        # Orchestrator skill
    └── <phase>/SKILL.md      # One per phase
```

## Source of truth: `docs/design/`

`docs/design/` is the canonical specification for what the plugin does. Files in `skills/` are the *implementation* of that spec.

### Convention: design first, then SKILL.md

When changing plugin behavior:

1. Open the relevant file under `docs/design/` (often `docs/design/phases/<phase>.md`).
2. Update the spec to reflect the desired behavior.
3. Update the corresponding `skills/<phase>/SKILL.md` to match.
4. Commit both changes together.

This convention is intentional. There is no automated build that enforces parity — keeping spec and implementation in sync is a code-review concern. Treat `docs/design/` as the contract and `SKILL.md` as the conformant implementation.

### Routing: where to look first

- **"How does the plugin behave at a high level?"** → [docs/design/README.md](docs/design/README.md)
- **"Why is the plugin shaped this way?"** → [docs/design/01-decisions.md](docs/design/01-decisions.md)
- **"What does phase X do?"** → `docs/design/phases/<phase>.md`
- **"What's the state.json schema?"** → [docs/design/05-state.md](docs/design/05-state.md)
- **"How is it distributed/installed?"** → [docs/design/04-distribution.md](docs/design/04-distribution.md)
- **"What's intentionally out of scope?"** → [docs/design/07-open-items.md](docs/design/07-open-items.md)

## Plugin packaging

- Plugin name: `ccw`
- Skills are namespaced as `/ccw:<skill>` (the folder name under `skills/` becomes the skill name)
- Version is explicit semver in `.claude-plugin/plugin.json`; bump it manually for each release

## Local development

```
claude --plugin-dir /path/to/this/repo
```

Or `/reload-plugins` inside an active Claude Code session to pick up edits without restarting.

## Common tasks

- **Add a new phase**: update `docs/design/02-architecture.md`, add `docs/design/phases/<new>.md`, update transitions and state values, then add `skills/<new>/SKILL.md`.
- **Change a phase's behavior**: edit `docs/design/phases/<phase>.md`, then `skills/<phase>/SKILL.md`.
- **Cut a release**: bump `version` in `.claude-plugin/plugin.json`, commit, push.

## What lives elsewhere (not in this repo)

When the `ccw` plugin is *used* (in some other project), it creates:

- `.claude/workflow/<feature-name>/state.json` — progress state for an in-progress feature
- `.claude/workflow/<feature-name>/config.json` — verification commands for that feature
- `docs/features/<feature-name>/design.md` and `plan.md` — feature artifacts

These paths are mentioned here for context only. They never appear in this repo.
