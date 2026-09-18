# Use cases
## `CLAUDE.md` vs Skills
### Use `CLAUDE.md` for
- Project-wide standards that always apply
- Constraints like "never modify the database schema"
- Framework preferences and coding style
### Use Skills for
- Task-specific expertise
- Knowledge that's only relevant sometimes
- Detailed procedures that would clutter every conversation
## Skills vs Subagents
- Skills add knowledge to your current conversation. When a skill activates, its instructions join the existing context.
- Subagents run in a separate context. They receive a task, work on it independently, and return results. They're isolated from the main conversation.
### Use Subagents when
- You want to delegate a task to a separate execution context
- You need different tool access than the main conversation
- You want isolation between delegated work and your main context
### Use Skills when
- You want to enhance Claude's knowledge for the current task
- The expertise applies throughout a conversation
## Skills vs Hooks
- Hooks fire on events. A hook might run a linter every time Claude saves a file, or validate input before certain tool calls. They're event-driven.
- Skills are request-driven. They activate based on what you're asking.
### Use Hooks for
- Operations that should run on every file save
- Validation before specific tool calls
- Automated side effects of Claude's actions
### Use Skills for
- Knowledge that informs how Claude handles requests
- Guidelines that affect Claude's reasoning