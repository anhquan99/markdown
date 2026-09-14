# Subagent
## Definition
- Every time you chat with Claude Code, you're adding to the main context window. The context window is finite, and once it fills up, Claude starts losing track of earlier parts of the conversation.
- Subagents solve this by spinning up a separate context window. The subagent receives two things:
	- **A custom system prompt** from your configuration file that defines the subagent's role and behavior
	- **A task description** written by the parent agent based on what you asked for
- When it's done, only a summary comes back to your main conversation. The entire subagent conversation is then discarded.
## Example scenario
- Say you're exploring an unfamiliar codebase, and you want to know which service handles refunds. Without a subagent, Claude might read 15 files, run several searches, and trace through multiple function calls. All of that fills your context window, even though you only needed one fact.
- With a subagent, the experience is much cleaner. You ask the question, the Explore subagent spins up, does all that digging in its own context, and hands back a focused answer.
- Your main context window only records the question and the summary: not the 15 files that were read along the way.
## Bult-in subagents
- **General purpose subagent**: for multistep tasks that require both exploration and action
- **Explore**: for fast searching and navigation of codebases
- **Plan**: used during plan mode for research and analysis of your codebase before presenting a plan
## Creating subagent
- Use command `/agents`.
- Scope:
	- Project-level
	- User-level
- Pick a color to show in the UI so you can quickly tell which subagent is active.
### Component in subagent definition file
- **`name`**: A unique identifier for the subagent. This is how you reference it, either by asking Claude directly or by typing `@agent code-quality-reviewer` in your message.
- **`description`**: Controls when Claude decides to use the subagent. This must be a single line (use escaped newline characters `\n` if you need breaks). You can include example conversations here to help Claude understand when delegation is appropriate.
- **`tools`**: Lists which tools the subagent can access. This matches whatever you selected during generation, but you can edit the list here at any time.
- **`model`**: Specifies which Claude model to use.
- **`color`**: The UI color for identifying the subagent.
```ad-note
Add **proactively** to the description if you want to delegate the task without asking.
```
## Design effective subagent
- **Specific descriptions**: The description controls when the subagent is launched and what instructions it receives. Write it to steer both.
- **Structured output**: Define an output format in the system prompt so the subagent knows when it's done and returns information the main thread can use.
- **Obstacle reporting**: Include a section in the output format for workarounds, quirks, and problems so the main thread doesn't have to rediscover them.
- **Limited tool access**: Only give a subagent the tools it actually needs. Read-only for research, bash for reviewers, edit/write only for agents that should change code.
## Using effective subagent
- **The key question: does the intermediate work matter? If not, then delegate it.**
- Subagents excel at tasks where:
	- You need a result, not a play-by-play of how it was found
	- The exploratory work would clutter your main thread's context
	- The task benefits from a fresh perspective or a custom system prompt
### Example tasks
- Research
- Code review
### Bad practices
- Expert claims
- Sequential Pipelines: Pipelines work when tasks are truly independent. They fail when each step depends on discoveries from the previous step -- and bug fixing almost always does. Information gets lost in the handoff between agents.
- Test runners