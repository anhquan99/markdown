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
- **PreToolUse** — runs before a tool call
- **PostToolUse** — runs after a tool call completes
- **UserPromptSubmit** — runs when you submit a prompt, before Claude processes it
- **Stop** — runs when Claude finishes responding
- **Notification** — runs when Claude sends a notification
![](/image/Pasted%20image%2020260913180808.png)
## Exit codes
- **Exit code 0** — proceed normally.
- **Exit code 2** — block the action. The stderr message gets fed back to Claude as feedback so it knows why it was blocked and can adjust.
- **Any other exit code** — a non-blocking error that gets shown to you but doesn't stop anything.