# `CLAUDE.md`
## Definition
- `CLAUDE.md` is a Markdown file you add to the root of your project, and Claude Code reads it automatically every time you start a session.
- Think of it as an onboarding script for your codebase.
- The contents of the `CLAUDE.md` file are appended to your prompt.
- `CLAUDE.md` loads into every conversation, always.
## Consideration
- Before you write a rule, ask whether it belongs in `CLAUDE.md` at all. Some rules are guidance, and some rules are hard lines that must never be crossed. Those are two different jobs.
- Take a rule like "never push to main." If you put that in `CLAUDE.md`, you're hoping Claude reads it and respects it. Most of the time it will. But "most of the time" isn't good enough for something that dangerous. A hard rule like that belongs in a pre-tool-use hook instead.
- The difference matters. A hook is code that runs before Claude takes an action, and it can actually block the action. So even if Claude does try to push to main, the hook stops it. That's real enforcement, not a polite request.
- Move your hard rules to hooks and let `CLAUDE.md` handle the softer conventions.
## Hierarchy
- **Project-level `CLAUDE.md`** lives in the root directory of your project. Shared with the team.
- **User-level `CLAUDE.md`** lives in your configuration folder. This one is just for you and applies across all your projects. Put your personal preferences here.
### Locations
- **Managed policy** — the org-level file your platform team controls. You can't exclude it, so org policy is always in play.
- **User** — your personal preferences that follow you across every project on your machine.
- **Project** — the file shared with your team, checked into the repo.
- **Local** — ignored by git. Your personal notes for this one repository only.
## Tips
- **Save corrections to memory.** If you find yourself correcting Claude repeatedly — like telling it to always use server actions instead of API routes — explicitly ask Claude to save that rule to memory. Next time you open the project, it'll know.
- **Reference project docs.** If you have documentation in your project that you want Claude to reference, use the `@` symbol with the file path:
- **Start without one.** It's recommended to start a project without a `CLAUDE.md` file so you can see where you constantly have to course-correct the model. This keeps your `CLAUDE.md` compact and focused on only the necessary information. When you're ready, run `/init` to have Claude generate one for you.
## Usage
### Split up a big file with imports
- When your project file starts getting long, you can break it into pieces using the path-to-file import syntax. Instead of one wall of text, you point to other files.
- This is great for organizing, but everything still loads up front.
```claude
@.claude/conventions/code-style.md
@.claude/conventions/testing.md
@.claude/conventions/workflow.md
```
### Phrasing is what makes rules stick
#### Be specific and checkable.
- Vague: _"Follow best practices for API routes."_
- Specific: _"Put new API routes in `src/api/handlers`, one per file."_
#### Name the replacement, don't just ban something
- When you tell Claude not to do something, say what to do instead.
- Leaves it open: _"Don't use default exports."_ Okay, but then what?
- Closes it: _"Use named exports, not default exports."_
#### Emphasis is a budget
- Words like **IMPORTANT** and **YOU MUST** do raise a rule's priority. But only relative to everything quieter around it. If every rule shouts, then nothing stands out and the emphasis means nothing. So treat emphasis like a budget. Spend it on the two or three rules that really hurt when they get broken, and let the rest sit at normal volume.
#### Keep the file under revision
- Your `CLAUDE.md` file is never finished. Treat it like living code that keeps getting edited.