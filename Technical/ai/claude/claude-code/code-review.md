# Code Review
## Managed path
- Anthropic-hosted service that reviews your pull requests through the Claude GitHub app.
- The admin installs the Claude GitHub app, picks which repos it watches, and decides when it runs.
- Trigger points:
	- Once when a PR opens
	- On every push to the PR
	- Only when someone comments `@claude review`
## Consideration
- It never approves or blocks the PR. The judgment call stays with a human. Claude flags things; you decide.
- There's no managed autofix. The service posts findings only.
- It's a research preview right now, available on team and enterprise plans, so expect the behavior to keep moving.
### Fix PR flow
- From your own terminal, the `/code-review` command reviews a diff, and its `--fix` flag applies the findings to your working tree.
- Claude finds it in the PR, you pull it down and fix it locally.
## DIY path
- Setup starts inside Claude Code. Run the `/install-github-app` command. You'll need repo admin to do this. The slash command walks you through installing the GitHub app and setting the Anthropic API key secret on the repo.
- The action itself is `anthropics/claude-code-action@v1`. Here are the inputs you'll actually use:
	- `anthropic_api_key` — optional.
	- `github_token` — defaults to `secrets.GITHUB_TOKEN`.
	- `trigger_phrase` — what the action listens for in comments. Defaults to `@claude`.
	- `use_bedrock` / `use_vertex` — switch to those providers if you're on Bedrock or Vertex.
	- `prompt` — the instruction for the run.
	- `claude_args` — a string of CLI arguments passed straight through to Claude Code.
### Workflow
- Drop a workflow into `.github/workflows/claude.yaml` and it listens for `@claude` on PR comments and issue comments.
```yaml
- uses: anthropics/claude-code-action@v1
  with:
    anthropic_api_key: ${{ secrets.ANTHROPIC_API_KEY }}
    github_token: ${{ secrets.GITHUB_TOKEN }}
    trigger_phrase: "@claude"
    prompt: "Your instructions here"
    claude_args: "--max-turns 5 --model claude-sonnet-5"
```
- Now someone writes `@claude implement the spec in the linked Linear issue` on a pull request, and the action picks it up. Claude pushes commits and posts comments describing what it did.
#### A workflow that runs on a schedule
- The same action works for a daily rollup. A cron trigger fires at, say, 9:00 UTC, the action runs, and Claude posts the results. You can also add a `workflow_dispatch` trigger so you can kick it off manually from the Actions tab.
- When the action runs, you can watch it work through the steps in the Actions tab, just like any other GitHub workflow.
```ad-note
- For PR reviews, take the managed path. Enable Code Review, let the GitHub app post inline findings, and apply fixes locally with `/code-review --fix`.
- Reach for the action when the job is more than review. Use `/install-github-app` for setup, one workflow for `@claude` mentions, one for cron, and all the tuning lives in `claude_args`.
```