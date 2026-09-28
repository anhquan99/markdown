# Feedback Loop
- Always give Claude a way to verify its own work, whether tests, a build, or a screenshot diff. A session checks its own work and fixes its own mistakes before an engineer sees them.

|Traditional|AI-native|
|---|---|
|The signal that code works arrives late. CI minutes later, a tester days later, production weeks later. With an agent producing the code, a late signal means a person has to check all of its output, and that person becomes the bottleneck.|The session is given a way to check its own work before a person sees it. Run the tests, run the build, take the screenshot. Claude iterates until the check passes, so what reaches the engineer has already passed it. Setting the loop up falls to the engineer running the session, and the steps below are written for them.|
## Execution
1. If checking the work today takes a sequence of commands and some environment knowledge, wrap it in a single target such as `make test` or `npm test` that exits non-zero on failure.
2. In the `CLAUDE.md`'s Commands section, list each command with an example of a healthy output.
3. State a target and make it quantifiable so Claude can check the work without asking you, for example: "All tests in `test_status.py` pass," "the screenshot matches the attached mock," or "the endpoint returns 200 with the new field."
4. For bug fixes, write the failing test first. Ask Claude to reproduce the bug as a test, run it, and confirm it fails for the reason you expect. Commit that test. Only then ask Claude to make it pass without editing the test, with the test-file hook from the final step enforcing the restriction. A test that existed before the fix, and that the agent couldn't rewrite, is proof the bug is gone.
5. For UI work, close the loop with a visual check. Give Claude a browser or screenshot tool, give it the mock, and let it iterate. Implement, screenshot, compare, and adjust. Two or three rounds is normal, and the result should improve with each one.
6. Make verification part of "done." The instruction lives in `CLAUDE.md`: "Run the tests before reporting a task complete, and show the output."
7. Finally, the loop itself needs protecting, because an agent fixing code must not be able to weaken the check on that code. A hook that blocks edits to test files during a fix task does this. The alternative is to check the diff in review and reject any change that touches a test.
## Example
- `CLAUDE.md`:
```mardown
## Verifying your work

- Build: make build (must finish with "Build succeeded")
- Test: make test (all green; never skip or delete a failing test)
- Lint: make lint (zero warnings)

Run all three before reporting any task complete, and paste the output.
If a test fails, fix the code, not the test.
```
## How to measure it
- **Leading indicator**: First-pass CI success rate for agent-written changes, which the CI system already supports.
- **Lagging indicator**: Review time per PR (from the PR metadata), which should fall once the tests catch what reviewers used to catch, and the change failure rate from an incident tracker.