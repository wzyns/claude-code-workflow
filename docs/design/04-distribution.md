# Distribution and Updates

The plugin is hosted in this GitHub repository. No package registry, marketplace server, or other infrastructure is required — the same repo that holds the source also acts as the marketplace listing.

## Plugin Manifest (`.claude-plugin/plugin.json`)

Contains the plugin name (`ccw`), an explicit semver version, description, author, and repository URL. The version is bumped manually whenever a release is intended.

## Marketplace Listing (`.claude-plugin/marketplace.json`)

Declares this repository as a marketplace and lists the single plugin (`ccw`) with `"source": "."`. This lets users add the marketplace once and install the plugin from it.

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
