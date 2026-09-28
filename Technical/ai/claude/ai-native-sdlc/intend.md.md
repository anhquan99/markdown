# `intend.md`
- Kicks off the software development process, can enter through different routes.
- When a person has an idea, they brainstorm with Claude and produce a Markdown proto-spec. In the traditional SDLC, the same person must then convince a member of the product team to write the idea up with them or on their behalf.
- Usually approved by product owner.

| Traditional                                                                                                                                                                                                                                        | AI-native                                                                                                                                                                                                                                      |
| -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| An idea passes through backlog entries, user stories, story points, and refinement meetings before anyone can act on it. Ownership transfers at each handoff, so what reaches engineering is several steps removed from what the originator meant. | The originator brainstorms with Claude and writes the result down as `intent.md`, a proto-spec in the originator's own terms. The artifact contains what is wanted, why, and under which constraints. Repeat processes are encoded via skills. |
## Execution
1. The originator describes the problem to Claude in their own words. The originator may describe what they cannot do today, who is affected by the idea, what better looks like, or what is out of scope. No formal language is required.
2. Brainstorm until the idea is concrete. Claude asks the questions an analyst would ask: scope, users, constraints, and what success looks like.
3. Ask Claude to write the result as `intent.md` using the organization's template, which can be encoded as a skill set up by a technical team member and signed off by a lead. This can cover the problem, proposed outcome, affected users and systems, constraints, and open questions.
4. The originator corrects anything Claude misunderstood.
5. Commit `intent.md` to the shared home. Author and timestamp join the record, and the product owner picks the idea up from there.
## Example
```markdown
# Intent: claims status self-service
Author: J. Ortiz (claims operations). Status: draft.
## Problem
Customers phone the contact center to ask where their claim is.
Handlers spend roughly a third of call time on status-only queries.
## Proposed outcome
Customers see claim status, next step and expected date in the portal.
## Affected users and systems
Claims handlers, portal team, claims-core API.
## Constraints
No new PII in the portal session. Existing authentication only.
## Open questions
Do third-party loss adjusters need access too?
```
## How to measure it
- **Leading indicator**: Time from first conversation to a committed `intent.md`, read from Git history on the intent home, which records author and timestamp. The expectation is for this to fall from a multi-week elicitation and refinement cycle to hours.
- **Lagging indicator**: The survival rate, or the share of `intent.md` files that the product owner accepts into **Stage 2: Design** rather than closes. The accept or reject decision is recorded as the merge of the artifact or the closed review. Additionally, count the changes to `intent.md` made after the first `spec.md` commit for the same change.