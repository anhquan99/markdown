# Claude
## Definition
- It's an AI assistant designed to be your thinking partner.
## Things Claude good at
- Writing and content creation
- Research and analysis
- Coding assistance
- Problem-solving and reasoning
- Learning new things
## Permission modes
- **Manual** reads only, without prompting. Everything else asks first.
- **Accept edits** runs reads, file edits, and common file system bash commands without asking. This is for iterating on code that you review after the fact.
- **Plan** reads only. It researches and proposes changes without editing anything.
- **Auto** accepts everything, with a separate classifier model reviewing each action before it runs.
- **Don't ask** allows only pre-approved tools. Everything else is auto-denied with no prompt.
- **Bypass permissions** skips all checks. This is the equivalent of the dangerously-skip-permissions flag. Only run it inside an isolated container or virtual machine.
### Auto mode
- Auto is the hands-off mode. Claude runs on its own, but before each action executes, a **separate classifier model reviews it**.
- The classifier guards intent. It's watching for moves that escalate beyond what you actually asked for.
### Don't ask, for unattended runs
- Don't ask is the right move whenever no human is around to approve prompts: CI pipelines, scheduled jobs, overnight batches.
- Only pre-approved tools are allowed, and anything off that list gets auto-denied with no prompt. That's the whole point. Your pipeline keeps moving instead of hanging on an approval no one is there to give.