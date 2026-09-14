# Verification
## Why?
- You handed Claude a task and let it run without watching every step. Now it says it's done. Before you ship that work, you need a way to check something you didn't even supervise. That check is what makes hands-off Claude Code safe to rely on.
- The idea here is simple: verify in proportion to how much rope you gave the run. If you watched the messages scroll by in a short session, a quick glance is enough. But an unattended run, or a job that fired in continuous integration with nobody in the loop, needs a real check. No one saw what happened, so you have to reconstruct it after the fact.
- The less you watched, the more you verify.
## Start with the diff, not the summary
1. Run `/code-review` to walk the changes and flag issues.
2. Then put your own eyes on `git diff`.
## Turn tests into a gate, not a promise
- The real gate on an unsupervised run is whether the tests passed, and whether Claude actually ran them or only claimed that it did. Don't leave that to trust. Wire it as a hook, so Claude can't skip it.
- A couple of hooks do the job:
	- A **stop hook** that runs your tests and refuses to end the turn on a failure.
	- A **post-tool-use hook** that lints and type checks after every edit.
- The key detail is the exit code. A hook that exits with `exit 2` feeds the failure straight back to Claude. Claude reads that failure and fixes it without you asking.
## Get a cold second opinion
- Open a fresh session or sub-agent and have it review the changed code with no memory of how the code was built.
- Because it has no stake in the approach, it catches the things the original run talked itself past.
- A second reviewer with fresh eyes finds what the author rationalized away.