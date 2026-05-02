# Document Phase

Skill: `/ccw:document`

Turn the agreements from the `design` phase into a polished markdown document.

## Input

- `state.json.notes` (or equivalent) from the `design` phase

## Behavior

1. Ask for the storage location (default `.claude/ccw/<name>/design.md`, or external such as Confluence).
2. Write the markdown document.
3. Ask the user to review and incorporate feedback.

## Output

- `design.md` (or content posted to an external destination)
- `state.json.config.design_doc_path` (or `design_doc_external`) updated
- `state.json.artifacts.design_doc` updated

## Exit Condition

- The user approves the document.

## Notes

- If the user picks an external destination, attempt to publish directly using an available tool (MCP server, CLI, etc.). If no such tool is available, output the markdown body so the user can publish manually.
- The document should be self-contained enough to onboard a new reader to the feature.
