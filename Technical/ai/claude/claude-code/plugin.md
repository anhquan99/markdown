# Plugin
## Definition
- A plugin is one installable unit. It bundles everything you'd otherwise share by hand: skills, subagents, hooks, and MCP server configs, plus the longer tail of stuff like language server protocol servers, background monitors, themes, and a slice of `settings.json`. One version, one install.
### Working with plugin
### Install
```claude
/plugin install github@claude-plugins-official
```
### Add to marketplace
- For a team, the better move is to add a private marketplace once. A marketplace is a shared source that plugins resolve through.
```claude
/plugin marketplace add your-org/claude-plugins
```
## Consideration
- Read before you install.