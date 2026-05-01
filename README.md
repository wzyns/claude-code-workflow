# claude-code-workflow

A Claude Code plugin (`ccw`) that turns feature development into a guided 8-phase workflow: design → document → plan → implement → verify → AI review → user review → PR.

## Install

In Claude Code:

```
/plugin marketplace add wzyns/claude-code-workflow
/plugin install ccw@claude-code-workflow
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

## Update

When a new version is released:

```
/plugin marketplace update claude-code-workflow
/plugin update
```

## Docs

- Plugin design: [docs/design/](docs/design/)
- Project conventions for Claude: [CLAUDE.md](CLAUDE.md)
