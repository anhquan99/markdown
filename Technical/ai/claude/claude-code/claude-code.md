# Claude Code
## Definition
- Claude Code has direct access to your files, your terminal, and your entire codebase. Instead of copying and pasting code back and forth, it goes in and does the work itself.
- The key differentiator is that Claude Code works as an [AI Agent](ai-agent.md)
## Capabilities
- Read and understand your codebase.
- Edit files across your project.
- Run terminal commands.
- Search the web.
## Effective use
- **The context window.** Think of this as Claude's working memory. It can hold a lot, but not everything at once. This is where the "agentic" aspect comes in — Claude finds strategic ways to locate answers within your codebase without loading the entire thing into context.
- You control its permissions.
- It can make mistakes.
## Plan mode
- Within the `Shift + Tab` menu is **Plan Mode**. Plan mode takes your prompt and uses read-only tools to analyze your codebase and research your suggested implementation. It will ask clarifying questions along the way, then return a detailed plan it can execute.
- Plan mode is great for planning complex changes or doing a safe code review. Many times you'll be asking Claude to handle multistep implementations toward a feature, and this is exactly where Plan Mode excels.
## Workflows
### Explore → Plan → Code → Commit workflow
#### Explore and plan
- Gives Claude the relevant context it needs for your project.
- Creates a plan of action that Claude uses to measure success.
- Claude will read relevant files, run some web searches, and give you a plan of action. Review it and decide if it meets your criteria. If not, ask it to revise specific areas.
#### Code
- The back and forth between you and Claude before settling on the final outcome.
##### Tips
- **Define a success criteria.** For Claude to be confident in its results, it needs to be clear on what "correct" looks like. Make this explicit when writing your plan.
- **Add tools.** Tools that help Claude complete its goals remove a lot of back and forth. For example, if you're building web UIs, install the Claude in Chrome extension so Claude Code can control a browser tab and test the UI directly.
- **Include a test suite.** Give Claude a test suite it can continuously validate against. Claude can even write tests for you. Before handing this off, make sure the tests are a reliable source of truth to avoid false positives.
#### Commit
- Helps you review and push your code so you can start on your next feature.
- Before you commit, run a **subagent code reviewer** to look at your work. A subagent gets a fresh pair of eyes on the codebase — it doesn't carry the bias the main agent might have from the session.
## Context management
- Reference [context window](ai-agent.md#Context).
![](/image/Pasted%20image%2020260913154748.png)
- When you approach the limit, the context window is automatically **compacted**. Compaction summarizes important details and removes unnecessary tool call results to free up space. Note that this process can potentially lose details.
### Commands for managing context
- `/compact`
	- Use when you're working on a specific feature and running up against the context limit but need to continue. Keeping the context relevant to your current feature is important.
- `/clear`: clear all the context window
	- Use when you want to start a new feature. You don't want the previous conversation to introduce bias into something new. For things you want Claude to remember across sessions, put them in your `CLAUDE.md` file so it doesn't have to rediscover things from scratch.
- `/context`: check state of the context
- `/diff`
- `/code-review`: reviews the change in a clean context, with none of your session's history, and reports what it finds. It edits nothing unless you ask it to.
	- `/code-review low`: review with low effort
	- `/code-review high`: review with hight effort
- `/rewind`: rewind the code change from the prompt
- `/agent`
### Tips for saving context space
- **Be specific.** A vague prompt might seem smaller, but it actually costs more context in the long run. Without clear instructions, Claude is forced to explore your codebase more and do its own reasoning — which takes up far more context space than a detailed prompt would.
- **Manage your MCP servers.** MCP servers load all of their available tools into context by default, even when you're not using them. If you have servers configured for things unrelated to the current project, consider turning them off. You can also try "Skills," which work similarly to MCP servers but don't load everything into context upfront.
- **Use subagents.** Subagents run in parallel with your main agent but have a completely separate context window. For tasks where you only need the answer — like "where are the authentication endpoints located?" — a subagent does the work and returns just a summary to your main agent, keeping your primary context clean.
## Code review note
- **Changes you didn't ask for.** A config value that was edited while Claude was in the file or a rewritten helper method that you didn't mention.
- **Tests that got weaker.** If the project you're working in contains tests, identify any that were skipped, deleted, or loosened until they passed.
- **New packages and hard-coded values.** A dependency that was added for only one function, a URL or a key written straight into the code.
### Action with code review findings
- **Fix now.** This is a real problem, it matters, and it must be fixed.
- **Ask why.** This is the pile for findings you can't quite verify or that seem off. It's possible for reviewers that are reading code changes cold to also miss things.
- **Leave it.** This is a real problem but small or inconsequential. These can often be batched into a group of fixes that you'll cover in one future session.
