# Skills
## Definition
- Skills are folders of instructions, scripts, and resources that Claude loads dynamically to improve performance on specialized tasks.
- A skill is a markdown file that teaches Claude how to do something once, and Claude applies that knowledge automatically whenever it's relevant.
- Think of them as expertise packages:they teach Claude how to complete specific tasks repeatably.
- Claude Code loads skills at startup, so restart your session after creating one. You can verify it's available by checking the available skills list.
```ad-note
Unlike **slash commands** (which require manual typing), **Skills are automatically applied** when Claude recognizes the situation. They're best for specialized, task-specific knowledge:like your team's code review standards, commit message formats, or brand guidelines. If you find yourself repeatedly explaining the same thing to Claude, that's a signal to create a skill.
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
- **Projects** are knowledge hubs. They hold the reference materials Claude needs to understand your work:project specs, meeting notes, research documents. When you upload files to a project, Claude draws on that information across every conversation within that project.
- **Skills** are procedural machines. They encode _how_ Claude should execute a task:the specific steps, order of operations, and methodology you want followed every time. Skills shine when you have repeatable workflows you want Claude to run consistently.
## Hierarchy priority
1. **Enterprise** : managed settings, highest priority
2. **Personal** : your home directory (`~/.claude/skills`)
3. **Project** : the `.claude/skills` directory inside a repository
4. **Plugins** : installed plugins, lowest priority
## Skill folder
- Drop a `reference.md` next to the skill for detailed material, then link to it from `skill.md`. Claude only reads it when it actually needs that depth. Your main file stays short.
- Put scripts in the folder too. Claude executes them rather than loading their contents into context. That means a skill can carry its own tooling, like a `check.sh` that runs all the gates.
```ad-note
Keep `skill.md` itself lean. Push the heavy material, the long explanations and the executable scripts, into side files. The lean file describes what to do; the side files hold the depth and the tools.
```
## How Skill Matching Works
- When Claude Code starts, it scans four locations for skills but only loads the **name and description** : not the full content. This is an important detail.
- When you send a request, Claude compares your message against the descriptions of all available skills. For example, "explain what this function does" would match a skill described as "explain code with visual diagrams" because the intent overlaps.
- Once a match is found, Claude asks you to confirm loading the skill. This confirmation step keeps you aware of what context Claude is pulling in. After you confirm, Claude reads the complete `SKILL.md` file and follows its instructions.
![](/image/Pasted%20image%2020260918073657.png)
## Configuration
### Required fields
- `name`
- `description`
### Metadata fields
- `allowed-tools` (optional): Restricts which tools Claude can use when the skill is active.
- `model` (optional): Specifies which Claude model to use for the skill.
### Effective description
- A good description answers two questions:
	- What does the skill do?
	- When should Claude use it?
- If your skill isn't triggering when you expect it to, try adding more keywords that match how you actually phrase your requests. The description is what Claude uses to decide whether a skill is relevant, so the language matters.
### Progressive disclosure
- Skills share Claude's context window with your conversation. When Claude activates a skill, it loads the contents of that `SKILL.md` into context. But sometimes you need references, examples, or utility scripts that the skill depends on.
- The open standard suggests organizing your skill directory with:
	- `scripts/`: Executable code
	- `references/` : Additional documentation
	- `assets/` : Images, templates, or other data files
- Then in `SKILL.md`, link to the supporting files with clear instructions about when to load them.
### Using scripts
- Scripts in your skill directory can run without loading their contents into context. The script executes and only the output consumes tokens. The key instruction to include in your `SKILL.md` is to tell Claude to _run_ the script, not _read_ it.
- It is useful for:
	- Environment validation
	- Data transformations that need to be consistent
	- Operations that are more reliable as tested code than generated code