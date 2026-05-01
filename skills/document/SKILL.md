---
name: document
description: Second phase of the ccw workflow. Turn the agreed design from the design phase into a polished markdown document. Asks the user where to save it (default docs/features/<feature>/design.md, or an external destination like Confluence). Use this when the user invokes /ccw:document directly, or when the orchestrator (/ccw:start) delegates the document phase.
---

# /ccw:document — Design Documentation

Turn the notes accumulated during the `design` phase into a polished, self-contained markdown document.

## Inputs
- `state.json.notes` — agreed design points
- `state.json.feature_name`

## Procedure

1. Read state.json. If `notes` is empty, warn the user and suggest running `/ccw:design` first; do not proceed without their explicit confirmation.
2. **Ask the user where to save the document**:
   - Default: `docs/features/<feature-name>/design.md`
   - External destination (Confluence, Notion, etc.) — in this case you will print the markdown body for the user to publish manually
3. Compose the markdown document. Suggested structure:
   - Title (feature name)
   - Goal
   - Requirements
   - Constraints
   - Approach (high level only — leave detailed steps for the `plan` phase)
   - Alternatives Considered
   - Trade-offs
   - Open Questions (if any)
4. Save to the chosen path (or print to chat for an external destination).
5. Ask the user to review. Iterate on feedback until the user approves.
6. Update state.json:
   - `config.design_doc_path` = saved path (or set `config.design_doc_external` for external)
   - `artifacts.design_doc` = same value
   - `updated_at` refreshed

## Output
- A markdown design document, saved or printed

## Exit condition
The user approves the document.

## What NOT to do
- Don't include implementation steps — that's the `plan` phase.
- Don't auto-publish to external systems on the user's behalf. Output markdown for the user to copy/paste.
- Don't skip the user-review iteration step — even if the document looks complete on first pass.
