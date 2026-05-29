# Document Phase

Skill: `/ccw:document`

Turn the agreements from the `design` phase into a polished markdown document.

## Input

- `state.json.notes` (or equivalent) from the `design` phase

## Behavior

1. Ask for the storage location (default `.claude/ccw/<name>/design.md`, or external such as Confluence).
2. Write the markdown document and save/publish it.
3. Report the saved location to the user — file path for local, URL/link for external destinations.

## Output

- `design.md` (or content posted to an external destination)
- `state.json.config.design_doc_path` (or `design_doc_external`) updated
- `state.json.artifacts.design_doc` updated

## Exit Condition

- The document is saved (or published to the external destination) and the location reported to the user.

## Notes

- If the user picks an external destination, attempt to publish directly using an available tool (MCP server, CLI, etc.). If no such tool is available, output the markdown body so the user can publish manually.
- The document should be self-contained enough to onboard a new reader to the feature.
- Do not ask the user to review the document content. Reporting the save location is the only user-facing acknowledgement. If the user wants to revise after the fact (whether the skill ran via the orchestrator or directly), they can re-invoke `/ccw:document`.
