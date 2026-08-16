# claude-code-workflow

This is my personal Claude Code workflow for developing a feature, packaged as a plugin (`ccw`) so I can use it across machines. It guides feature work through 6 phases:

1. **Design** — Talk through the feature in conversation to clarify requirements, constraints, alternatives, and trade-offs. Ends by saving the agreed design as a markdown doc at `.claude/ccw/<name>/design.md`.
2. **Plan** — Decompose the work into independently verifiable steps, each with an objective, scope, and verification method. Saves the plan to `.claude/ccw/<name>/plan.md` and creates the feature branch.
3. **Implement** — Carry out the plan one step at a time. Each step is a mini-cycle: implement → run unit tests → user review → commit. One commit per approved step keeps the PR easy to read.
4. **AI review** — The assistant auto self-reviews the commits recorded in `state.json.notes` for this workflow and emits structured findings to `artifacts.ai_review_findings`. No prompt and no fix commits at this stage — decisions about which findings to apply happen in user review.
5. **User review** — Present a structured summary of all changes plus the AI review findings for a final human review. The user decides per-finding whether to reflect, skip, or defer, and any last feedback is applied before the change becomes externally visible.
6. **PR** — Create the pull request following Claude Code's PR protocol, then add development notes (trade-offs, known limitations, follow-ups) as PR comments so reviewers get the accumulated context.

You can run the full orchestrator (`/ccw:start`) which walks you through every phase in order, or invoke any single phase directly.

## Install

In Claude Code:

```
/plugin marketplace add wzyns/claude-code-workflow
/plugin install ccw@claude-code-workflow
/reload-plugins
```

## Usage

Start a new feature workflow:

```
/ccw:start
```

The orchestrator walks you through each phase in order. You can also invoke any single phase directly:

```
/ccw:design /ccw:plan /ccw:implement
/ccw:ai-review /ccw:user-review /ccw:pr
```

## Workflow state

When you run `ccw` in a project, it creates `.claude/ccw/<feature>/` with:

- `state.json` — progress state, changes frequently
- `design.md` / `plan.md` — the design and plan documents

To keep this metadata out of git, add the following to your `.gitignore`:

```
.claude/ccw/
```

If you'd rather track it (to share workflow state with a team or resume on another machine), leave it as is. `ccw` does not modify your `.gitignore`.

## Update

When a new version is released:

```
/plugin marketplace update claude-code-workflow
/plugin update
/reload-plugins
```

## Docs

- Plugin design: [docs/design/](docs/design/)
- Project conventions for Claude: [CLAUDE.md](CLAUDE.md)
