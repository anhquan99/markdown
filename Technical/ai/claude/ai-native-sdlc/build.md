# Build
- Stage 3.
## Plan
- Engineers start Claude Code sessions in plan mode, give Claude the approved `spec.md` from **Stage 2: Design**, and let it interview them, iterating on the plan until they are happy with it.

| Traditional                                                                                                                                                                                                                                                                                       | AI-native                                                                                                                                                                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| An engineer reads the design and starts writing code. How the change will be made, down to which files and which tests, stays in the engineer's head or at best in a ticket comment. Nobody else can review it. The first thing a reviewer sees is the finished diff, and by then rework is slow. | Work starts with a written plan that Claude produces in plan mode, where it can read the codebase without changing anything. The engineer corrects the plan before code is written, and the approved version is committed as `plan.md` for later stages to check against. |
### Execution
1. The engineer starts the session in plan mode with Claude.
2. The engineer gives Claude the `intent.md` and the `spec.md` and asks for an implementation plan that names the files that change, the order of the work, and the tests that prove it.
3. Interrogate the plan by asking what the change could break, which step is most risky, and what other options Claude chose not to do.
4. Iterate until an engineer who has never seen the conversation could implement the change from the plan alone.
5. Commit the approved plan as `plan.md`. The plan joins the audit trail, and the PR review play (**Stage 5: Deploy**) checks the eventual diff against it.
6. Accept the plan and let Claude implement. With a solid plan, the implementation is often a single pass.
7. When implementation departs from the plan, update `plan.md` in the same commit. Consider using a hook to enforce synchronization between the two.
### How to measure it
- **Leading indicator**: Share of changes that merge from the first implementation pass, and time from plan approval to merged PR with the required data within the PR metadata.
- **Lagging indicator**: Rework cycles per change, again from the PR metadata, and how often the merged diff still matches the committed `plan.md`.
## `CLAUDE.md`
### How to measure it
- **Leading indicator**: How often Claude repeats a mistake `CLAUDE.md` should have caught. The corrections or changes to the `CLAUDE.md` should be tracked within the Git history.
- **Lagging indicator**: Time to first merged PR for a new member of the team from PR history.
## Skills
### How to measure it
- **Leading indicator**: Time from the policy owner approving a policy change to the updated skill merging, taken from the PR on the skill folder.
- **Lagging indicator**: PR review findings that cite the policy, which should fall toward zero once the skill is applying the policy while the code is written. Where the findings don't fall toward zero, either the skill isn't triggering or its text has drifted from the official policy.
## Parallel sessions and subagents
|Traditional|AI-native|
|---|---|
|One engineer works one task at a time and spends a significant portion of their day/week on builds, tests, and reviews. Switching between tasks while waiting is possible, but the context switch is tiring enough that few people choose to.|One engineer runs several Claude sessions at once, each in its own worktree on its own task. Repeated jobs become subagents with their own context and tool limits. The engineer's job shifts to orchestrating and, eventually, to building and monitoring loops.|
### Execution
1. The engineer splits the work into tasks that touch different files, using the plan from the plan mode play (**Stage 3: Build**) to see where the work is independent. Tasks that share files run in a single session, one after another.
2. Each parallel task gets its own worktree, for example `claude --worktree feature-auth` in one terminal and `claude --worktree fix-rate-limit` in another. A worktree is a separate checkout on its own branch, which stops sessions colliding on files.
3. Two or three sessions is a sensible starting point. The practical ceiling is how many streams one person can review properly, so add sessions only while review is keeping up.
4. Turn repeated jobs into subagents, as defined in Markdown files in `.claude/agents/`, each with a name, a description of when to use it, and the tools it may touch. Examples include a code simplifier that strips needless complexity after the main agent finishes, a verifier that runs the app and checks behavior, and a researcher that explores the codebase and reports back without flooding the main context. Check the definitions into Git, so the whole team shares them.
### How to measure it
- **Leading indicator**: Concurrent sessions per engineer while review quality holds, counted from the OpenTelemetry export, and the share of the day spent steering rather than waiting.
- **Lagging indicator**: Changes merged per engineer per week read alongside the rework rate as determined per the PR history.