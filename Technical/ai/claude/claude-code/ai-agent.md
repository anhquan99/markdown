# AI Agent
## Definition
- An AI Agent is software that can interact with its environment and perform actions to complete a defined goal.
- At its core, this works by having a large language model operating in a loop in real time.
- AI Agents can have access to tools, external services, or even other AI Agents to help reach their goals.
## Agentic loop
![](/image/Pasted%20image%2020260913152614.png)
1. You enter a prompt into Claude Code.
2. Claude gathers the context it needs by interacting with the model, which returns text or a tool call that Claude Code can execute.
3. It takes action — for example, editing a file or running a command.
4. It verifies the results and determines whether they achieve what your prompt set out to do.
5. If they do, Claude finishes and waits for the next prompt. If they don't, it loops back and tries again until the results are complete and verifiable.
## Context
- Claude has a **context window** that determines how much of your conversation, file contents, command outputs, and more it can store and reference. Once you reach that limit, Claude Code compacts your conversation — automatically determining what it can remove or summarize to bring the context window back down to a usable size.
## Tools
- Tools are the backbone of how agents work. Most AI assistants simply take text in and return text out. Tools let Claude Code determine _when_ to execute code to get closer to completing a task. This could be a file-reading tool, a web search tool, or any number of other capabilities.
- Claude Code uses **semantic understanding** determining when to call a tool and how to use the output.
## Sub agents
- Claude spawns a subagent to handle a task.
- The subagent runs in parallel with its own context window, does all the work, and once finished, summarizes its findings and returns that summary back to Claude.
- The result: you get the answer you were looking for, without the entire journey it took to get there cluttering your main context.
### Customization
- **Persistent memory**: lets your subagent retain memory across conversations. This is great if you're using it consistently on the same projects.
- **Preload skills**