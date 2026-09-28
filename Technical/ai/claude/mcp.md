# MCP
## Definition
- Stands for **Model Context Protocol**, is an open standard that lets Claude Code connect to external tools and data sources without requiring you to write a bunch of tedious integration code.
- Those are the tools that give agents like Claude Code the ability to perform actions that help them complete tasks more effectively. This is different from typical AI, where you just get a text response back.
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
## How MCP works
- MCP shifts this burden by moving tool definitions and execution from your server to dedicated MCP servers.
- The MCP Server wraps up tons of functionality and exposes it as a standardized set of tools. Your application connects to this MCP server instead of implementing everything from scratch.
![](/image/Pasted%20image%2020260919104338.png)
## MCP client
- The MCP client serves as the communication bridge between your server and MCP servers. It's your access point to all the tools that an MCP server provides, handling the message exchange and protocol details so your application doesn't have to.
- MCP client is transport agnostic.
- The client and server exchange specific message types defined in the MCP specification.
### How it works
1. **User Query:** The user submits their question to your server
2. **Tool Discovery:** Your server needs to know what tools are available to send to Claude
3. **List Tools Exchange:** Your server asks the MCP client for available tools
4. **MCP Communication:** The MCP client sends a `ListToolsRequest` to the MCP server and receives a `ListToolsResult`
5. **Claude Request:** Your server sends the user's query plus the available tools to Claude
6. **Tool Use Decision:** Claude decides it needs to call a tool to answer the question
7. **Tool Execution Request:** Your server asks the MCP client to run the tool Claude specified
8. **External API Call:** The MCP client sends a `CallToolRequest` to the MCP server, which makes the actual GitHub API call
9. **Results Flow Back:** GitHub responds with repository data, which flows back through the MCP server as a `CallToolResult`
10. **Tool Result to Claude:** Your server sends the tool results back to Claude
11. **Final Response:** Claude formulates a final answer using the repository data
12. **User Gets Answer:** Your server delivers Claude's response back to the user
![](Pasted%20image%2020260919114506.png)