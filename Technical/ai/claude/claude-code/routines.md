# Routines
## Definition
- A routine is the most direct way to automate a task.
- There's no script and no server. It bundles three things: a prompt, the repository it works on, and any connectors it needs. Then it runs that bundle in the cloud whenever it's triggered.
- The key part is that the infrastructure is Anthropic's. There's no machine of yours staying on overnight, and there's no workflow file for you to maintain. You describe the job once and it just runs.
## Create routine
- Create from the web at `claude.ai/code/routines`.
- CLI with `/schedule`:
```claude
/schedule daily dependency audit at 9am
```
## Consideration
- **Routines are a research preview.** Behavior and limits will keep moving, so don't be surprised if things change.
- **A recurring schedule runs at most hourly.** If you need something more frequent, routines aren't the tool.
- **Each run starts from a fresh clone of your default branch and can only push to `claude/` prefixed branches** unless you loosen that per repo. This is the guardrail that keeps an autonomous run from rewriting main.
## Headless mode
- A way to run Claude Code non-interactively without a terminal UI by using the -p (or --print) flag, allowing it to run its full agent loop, print a result, and exit.
### Usage
#### Multi-step automation with sessions
- Capture the session's ID from the JSON output and resume it later.
```claude
claude --resume "$(jq -r .session_id /tmp/plan.json)"
```
#### Getting structured data back
- JSON output that you can pipe into databases or other scripts.
#### Deterministic runs for CI
- When CI needs the same results every single run, there's a mode built for that.
- The `--bare` flag gives you deterministic mode. It's the right choice when you're running Claude Code inside a pipeline, andyou want repeatable, predictable output rather than anything that varies run to run.
#### The Agent SDK: Claude Code inside your own app
- A library that embeds Claude Code inside your own TypeScript or Python applications.
- Expose a `query` function and the same primitives as the CLI. You pass a prompt plus options, like:
	- `allowedTools` to control what Claude can do,
	- a system prompt,
	- and a permission mode.
- Then you iterate over the messages Claude streams back and handle them however your app needs. It's the same engine as the CLI, just callable from inside your product.
```ad-note
- **Routines** are the default for repeat work. They run on Anthropic's infrastructure with nothing for you to host.
- **Headless mode with `-p`** is for when the job needs your pipeline and you want to pipe data through a script.
- **`--bare`** is for when CI needs the same results every single run.
- **The Agent SDK** is for when the work belongs inside your own product.
```