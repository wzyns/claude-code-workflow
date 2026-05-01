# Distribution and Updates

The plugin is hosted in this GitHub repository. No package registry, marketplace server, or other infrastructure is required — the same repo that holds the source also acts as the marketplace listing.

## Plugin Manifest (`.claude-plugin/plugin.json`)

Contains the plugin name (`ccw`), an explicit semver version, description, author, and repository URL. The version is bumped manually whenever a release is intended.

## Versioning Policy

Strict semver is awkward for an agentic plugin because behavior is partly non-deterministic. The contract is therefore split into two layers; the question to ask before bumping is *"would another machine want to know about this before pulling the update?"*

**Layer 1 — deterministic contract.** Treat changes here like ordinary semver:

- Slash command names and arguments
- `state.json` and `config.json` schemas (fields, names, allowed values)
- Artifact paths and formats (e.g., `docs/features/<name>/design.md`)
- Phase list and phase order

A change that removes/renames any of the above, or that breaks resuming an in-progress workflow created by an older version, is a **major** bump.

**Layer 2 — behavioral contract.** Prompt wording, internal procedure phrasing, and tone are normally **patch**. Changes that the user would *notice as a workflow-shape difference* — adding or removing a confirmation gate, changing a per-step cycle, adding a new optional step — are **minor**. If a behavioral change makes prior expectations wrong (e.g., a phase no longer asks for input it used to require), treat it as **major**.

**Quick rule of thumb:**

- No external-facing change → **patch**
- Workflow looks or feels different but old workflows still resume cleanly → **minor**
- Breaks resume, removes/renames a slash command, or changes a phase's externally observable contract → **major**

While the project is in `0.x`, breaking changes only require a **minor** bump (per semver §4); the rules above apply in full once the plugin reaches `1.0.0`.

## Marketplace Listing (`.claude-plugin/marketplace.json`)

Declares this repository as a marketplace and lists the single plugin (`ccw`) with `"source": "./"` (relative paths in marketplace.json must start with `./`). This lets users add the marketplace once and install the plugin from it.

## Installation (per machine)

```
/plugin marketplace add wzyns/claude-code-workflow
/plugin install ccw@claude-code-workflow
```

Plugins are enabled by default after install.

## Updating to a New Version

When a new version is released (version bumped + pushed to GitHub):

```
/plugin marketplace update claude-code-workflow
/plugin update
```

Because `version` is set explicitly, no machine receives changes until a release is cut.

## Local Development

While iterating on the plugin itself:

```
claude --plugin-dir /path/to/claude-code-workflow
```

Or run `/reload-plugins` inside an active session to pick up edits without restarting Claude Code.
