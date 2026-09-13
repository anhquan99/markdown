# MCP
## Definition
- Stands for **Model Context Protocol**, is an open standard that lets Claude Code connect to external tools and data sources.
- Those are the tools that give agents like Claude Code the ability to perform actions that help them complete tasks more effectively.
- This is different from typical AI, where you just get a text response back.
## Types
- **HTTP servers** are for remote services. These are hosted by the service provider and connect over the network.
- **Stdio servers** are for local processes that run on your machine.
### Add MCP
- Use `claude mcp add`
## Scoping servers
- **Local** — only available in the current project, just for you.
- **User** — available across all your projects.
- **Project** — uses a `.mcp.json` file that you check into version control so anyone on the codebase gets the exact same servers automatically.
## Context Costs
- MCP servers add tool definitions to your context window — even when you're not actively using them. If you have a lot of servers configured, this eats into your available context.
- You might also benefit from using a [Skill](skills.md) instead. A [Skill](skills.md) has a name and description loaded into context, and Claude only loads the full skill contents when it determines it needs to use it.
- If your MCP tools exceed 10% of your context window, Claude Code automatically switches to tool search mode, which discovers the right tools on demand — though this may not work as reliably.