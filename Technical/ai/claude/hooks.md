# Hooks
## Definition
- Hooks let you run commands at specific points in Claude Code's lifecycle.
- The key difference between hooks and everything else covered in this course is that hooks are **deterministic** — they always run.
- You configure them through the `/hooks` command inside Claude Code, or by editing `.claude/hooks/settings.json` directly.
## Common use cases
- Auto-formatting after file edits
- Logging all executed commands for compliance
- Blocking dangerous operations like modifying production files
- Sending yourself notifications when Claude finishes a task
## Common event
- **PreToolUse** fires before a tool call. This is your enforcement primitive. It's the one that can stop something before it happens.
- **PostToolUse** fires after a successful tool call. This is usually where auto-formatting or an auto-lint goes.
- **Stop** fires when Claude wants to end its turn. You can refuse and say "no, you're not done yet" if some condition isn't met. There's a matching **SubagentStop** for when a sub-agent finishes.
- **PreCompact** and **PostCompact** fire before and after compaction.
- **InstructionsLoaded** fires when a `CLAUDE.md` or rule file loads. Handy for auditing what actually made it into context.
- **SessionStart** fires at the start and primes the environment. Use the `startup` source if you only want it on fresh starts.
- **Notification** — runs when Claude sends a notification.
![](/image/Pasted%20image%2020260913180808.png)
## Return result
### `PreToolUse` return a decision as json
- The key field is `permissionDecision`, and it takes one of three values:
	- `allow`: let the call through
	- `deny`: stop the call
	- `ask`: hand it back to the user to decide
	- `defer`: only apply to non-interactive runs where a calling process pauses the tool and resumes it later.
```json
{
  "hookSpecificOutput": {
    "hookEventName": "PreToolUse",
    "permissionDecision": "deny",
    "permissionDecisionReason": "...",
    "updatedInput": {
      "command": "..."
    }
  }
}
```
### Exit codes
- **Exit code 0**: proceed normally.
- **Exit code 2**: block the action. The stderr message gets fed back to Claude as feedback so it knows why it was blocked and can adjust.
- **Any other exit code**: a non-blocking error that gets shown to you but doesn't stop anything.
## Preserving state across a compact
- When Claude compacts a long conversation, it drops a lot of detail.
- A SessionStart hook with the `compact` matcher runs right after compaction.
- Have it printed a short summary of the files you've been working on. That summary goes back into context, so Claude picks up where it left off instead of starting cold.