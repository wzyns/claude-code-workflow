---
name: document
description: Second phase of the ccw workflow. Turn the agreed design from the design phase into a polished markdown document. Asks the user where to save it (default .claude/ccw/<feature>/design.md, or an external destination like Confluence). Use this when the user invokes /ccw:document directly, or when the orchestrator (/ccw:start) delegates the document phase.
---

# /ccw:document — Design Documentation

Turn the notes accumulated during the `design` phase into a polished, self-contained markdown document.

## Inputs
- `state.json.notes` — agreed design points
- `state.json.feature_name`

## Procedure

1. Read state.json. If `notes` is empty, warn the user and suggest running `/ccw:design` first; do not proceed without their explicit confirmation.
2. **Ask the user where to save the document**:
   - Default: `.claude/ccw/<feature-name>/design.md`
   - External destination (Confluence, Notion, etc.) — in this case attempt to publish directly using an available tool (MCP server, CLI, etc.); if none is available, print the markdown body for the user to publish manually
3. Compose the markdown document. Suggested structure:
   - Title (feature name)
   - Goal
   - Requirements
   - Constraints
   - Approach (high level only — leave detailed steps for the `plan` phase)
   - Alternatives Considered
   - Trade-offs
   - Open Questions (if any)
4. Save to the chosen path. For an external destination, publish directly via an available tool (MCP server, CLI, etc.); if no such tool is available, print the markdown body to chat so the user can publish manually.
5. Report the saved location to the user — the file path for local saves, or the URL/link for external destinations (or, if external publishing was unavailable and the markdown was printed instead, say so).
6. Update state.json:
   - `config.design_doc_path` = saved path (or set `config.design_doc_external` for external)
   - `artifacts.design_doc` = same value

## Output
- A markdown design document, saved or printed
- A message to the user with the save location (path or link)

## Exit condition
The document is saved (or published) and the location has been reported to the user.

## What NOT to do
- Don't include implementation steps — that's the `plan` phase.
- Don't ask the user to review or approve the document content. Reporting where it was saved is the only acknowledgement needed — any revisions come from the orchestrator's phase-transition prompt or a re-invocation of `/ccw:document`.
