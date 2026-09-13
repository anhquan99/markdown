# Steering Long Sessions
## Problem
- Long tasks are a different game. The more you have to steer Claude along the way, the longer it drags on.
## Scope the work first with plan mode
- Before Claude writes a single line, get it to lay out a plan. In plan mode, Claude does its research in read-only mode. It reads the code, figures out what needs to change, and hands you a plan to review.
- When you get that plan, actually read it. Don't skim it. The more thorough the plan, the fewer surprises you'll hit once Claude starts executing. If something's off or missing, just ask Claude to add it where you want. Iterating on a plan is much faster than letting Claude run and hoping for the best, then cleaning up the mess.
## Steer while Claude works
### Compact
- Compact summarizes your conversation, uses that summary as the new context, and deletes the old messages. This frees up your context window, so Claude can keep going. The risk is that something important gets dropped in the summary, and Claude drifts off course.
- Add instructions after the command to tell Claude how to summarize.
### Rewind
- **Restore code and conversation** - roll back both together.
- **Restore conversation** - roll back just the chat.
- **Restore code** - roll back just the files.
- **Summarize from here** - summarizes everything after the checkpoint. Great if you had a side conversation and just want to free up some space.
- **Summarize up to here** - summarizes everything before the checkpoint. Great when you had a long setup phase you want to compress, but you want to keep the implementation parts intact.
## Let Claude run more autonomously
### Goal
- Goal sets a completion condition. You describe what "done" looks like, and Claude keeps working across turns until a fast evaluator confirms those conditions are met. It won't just stop the first time it thinks it's finished.
```claude
/goal all tests in src/billing pass, and the type checker reports zero errors
```
- To cancel it, run `/goal clear`. One important constraint: the evaluator only reads the transcript. So your condition has to be checkable from the output Claude actually produces, like the results of a test run.
### Loop
- Loop runs a prompt on an interval between turns, either fixed or self-paced. Use it to pull something external, like a CI run or a deploy, and act when the state changes.
- To stop a loop, just press escape.
### Run parallel work with worktrees
-   The steering metaphor so far assumes one steering wheel in one car. But when you're running multiple agents on the same codebase, you don't want two steering wheels in one car. That's unsafe. Two Claude sessions fighting over the same files leads to conflicts.
- That's where worktrees come in. Instead of sessions stepping on each other, each one gets its own independent file tree.
- Because each agent has its own tree, they can't clobber each other's changes. When a session exits, a clean worktree is automatically removed.
- There's one helpful file to know about. A `.worktreeinclude` file at the repo root lists git-ignored files to copy into each worktree. This is useful for things like an environment variable file or a local config that you need in every worktree but don't want to commit to version control.