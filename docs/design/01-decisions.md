# Design Decisions

This file captures the design decisions made for `ccw` and the reasoning behind each. These decisions are load-bearing: changing them likely requires re-evaluating multiple downstream design files.

| Item | Decision | Notes |
|------|----------|-------|
| Distribution format | Claude Code plugin (`ccw`) hosted on GitHub | Single-repo plugin + marketplace; no separate hosting needed |
| Plugin name | `ccw` | Skills are namespaced as `/ccw:<skill>` (e.g., `/ccw:start`, `/ccw:design`) |
| Version strategy | Explicit semver in `plugin.json`, manually bumped per release | Updates only roll out to other machines when version is bumped |
| Skill structure | Hybrid (orchestrator + per-phase sub-skills) | Each phase can be invoked independently and reused |
| Progress state storage | `.claude/ccw/<feature-name>/state.json` | Local metadata in the consuming project; gitignored |
| Artifact storage (default) | `.claude/ccw/<feature-name>/` | Keeps all per-feature ccw output in one directory; git tracking is the user's choice (see `.gitignore` guidance in README) |
| Artifact location prompt | Asked each time | Allows external destinations such as Confluence |
| Autonomy level | User confirmation at most phase transitions; `ai-review → user-review` auto-advances on success | Keeps the user in control where decisions matter, removes the prompt on a phase that has nothing for the user to decide |
| AI review tool | Assistant self-reviews the commits recorded in `state.json.notes` for this workflow and emits findings to `artifacts.ai_review_findings` | No prompt at the start; runs automatically. AI review no longer creates fix commits — decisions about which findings to apply are deferred to `user-review` |
| PR creation tool | `gh pr create` | Uses the standard Claude Code PR protocol |
| Per-phase verification commands | Asked once on first run, stored in `config.json` | Reused across the same feature |
