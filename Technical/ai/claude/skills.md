# Skills
## Definition
- Skills are folders of instructions, scripts, and resources that Claude loads dynamically to improve performance on specialized tasks.
- A skill is a markdown file that teaches Claude how to do something once, and Claude applies that knowledge automatically whenever it's relevant.
- Think of them as expertise packages—they teach Claude how to complete specific tasks repeatably.
```ad-note
Unlike **slash commands** (which require manual typing), **Skills are automatically applied** when Claude recognizes the situation. They're best for specialized, task-specific knowledge—like your team's code review standards, commit message formats, or brand guidelines. If you find yourself repeatedly explaining the same thing to Claude, that's a signal to create a skill.
```
## Types
- **Anthropic Skills** are created and maintained by Anthropic. These include enhanced document creation capabilities for Excel, Word, PowerPoint, and PDF files. Claude invokes them automatically when relevant, so you don't need to do anything special to use them.
- **Custom Skills** are ones you or your organization create for specialized workflows and domain-specific tasks. For example, you might create a skill that applies your company's brand guidelines to presentations, structures meeting notes in a specific format, or executes your organization's data analysis workflows.
## Security considerations
- Only install custom Skills from trusted sources
- Anthropic's built-in Skills are tested and maintained by Anthropic
- Custom Skills you upload are private to your individual account
- If you're installing a custom Skill from an external source, review its contents before use to understand what it does.
## Skills vs. Projects
- **Projects** are knowledge hubs. They hold the reference materials Claude needs to understand your work—project specs, meeting notes, research documents. When you upload files to a project, Claude draws on that information across every conversation within that project.
- **Skills** are procedural machines. They encode _how_ Claude should execute a task—the specific steps, order of operations, and methodology you want followed every time. Skills shine when you have repeatable workflows you want Claude to run consistently.
## Hierarchy
- **Personal:** `~/.claude/skills`
- **Project:** `.claude/skills` in the root of the project