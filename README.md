# claude-code-workflow

This is my personal Claude Code workflow for developing a feature, packaged as a plugin (`ccw`) so I can use it across machines. It guides feature work through 8 phases:

1. **Design** — Talk through the feature in conversation to clarify requirements, constraints, alternatives, and trade-offs. Produces an agreed summary, not yet a document.
2. **Document** — Turn the agreed design into a polished markdown doc (default `.claude/ccw/<name>/design.md`, or an external destination like Confluence).
3. **Plan** — Decompose the work into independently verifiable steps, each with an objective, scope, and verification method. On first run, captures the project's `test`/`lint` commands and creates the feature branch.
4. **Implement** — Carry out the plan one step at a time. Each step is a mini-cycle: implement → run unit tests → user review → commit. One commit per approved step keeps the PR easy to read.
5. **Verify** — Run integration-level checks against the complete implementation (integration tests, full suite, lint, type check) and diagnose/fix any failures.
6. **AI review** — Ask the user how they want to perform the AI review (self-review, sub-agent, external tool, or skip), then summarize the feedback together, decide which items to address, and apply changes.
7. **User review** — Present a structured summary of all changes for a final human review, and apply any last feedback before the change becomes externally visible.
8. **PR** — Create the pull request following Claude Code's PR protocol, then add development notes (trade-offs, known limitations, follow-ups) as PR comments so reviewers get the accumulated context.

You can run the full orchestrator (`/ccw:start`) which walks you through every phase and confirms before each transition, or invoke any single phase directly.

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

The orchestrator walks you through each phase and confirms with you before transitioning. You can also invoke any single phase directly:

```
/ccw:design /ccw:document /ccw:plan /ccw:implement
/ccw:verify /ccw:ai-review /ccw:user-review /ccw:pr
```

## Workflow state

When you run `ccw` in a project, it creates `.claude/ccw/<feature>/` with:

- `state.json` — progress state, changes frequently
- `config.json` — verification commands and other settings

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
