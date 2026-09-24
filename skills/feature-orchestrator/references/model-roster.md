# Model roster

Read this when the user asks for the Session 2 prompt. The roster question is part of every handoff: ask it after the user says to compile and before drafting the prompt.

The roster is the user's choice. Presets below are recommendations to offer, not decisions to make on the user's behalf.

## Roles

| Role | Runs as | Owns | What matters most |
| --- | --- | --- | --- |
| **Lead** | Session 2's main agent | Planning, decomposition, sequencing, integration, conflict resolution, triage of review findings, final verification, handoff | Judgment and long-horizon coherence |
| **Implementer** | Sub-agents in Session 2 | Bounded code changes, tests for those changes, remediation of accepted findings | Coding accuracy on a scoped brief |
| **Researcher** | Sub-agents in Sessions 1 and 2 | Documentation and API checks, read-only repository exploration, test-gap analysis | Breadth; many can run in parallel |
| **Reviewer** | Sub-agents in Session 2 | Independent, read-only review of the integrated change against distinct risk lenses | Skepticism and independence from the authors |

**The Lead is not part of the roster.** The Lead is whatever model and effort the user launches Session 2 with, so the skill neither asks about it nor prescribes it. The roster covers the three sub-agent roles.

Each sub-agent role is a model setting, not a headcount. Session 2 runs as many agents of each role as the work supports: several Researchers in parallel, several Implementers on independent slices, and several Reviewers each taking a different risk lens. The Lead can also implement directly when a change is small or too tightly coupled to split.

## Asking for the roster

Ask every time a prompt is compiled, even if a roster was used earlier in the conversation or in a previous one. Models, budgets, and the feature's risk change, and the user wants to decide per handoff.

1. **Prepare the options.** Detect the environment and check which models and effort levels are actually selectable. The recommended option is the user's last roster from this conversation or their saved preference, if either exists. Otherwise it is the environment preset.
2. **Ask one question per role** (Implementer, Researcher, Reviewer). Put the recommended option first so a single confirmation accepts all three.
   - With a structured question tool (for example `AskUserQuestion` in Claude Code), ask all three questions in one call with 2–4 options each. Every option combines model and effort, such as `opus · medium`. The tool's free-text "Other" covers anything unlisted.
   - Without one, send a compact table with the recommended option and ask the user to reply "ok" or edit any row.
3. **Skip only if the user already answered.** If the compile request itself spells out the roster, for example `compile it with impl=opus/medium research=opus/low review=opus/medium`, treat that as the answer. Each value is `model` or `model/effort`. Omitted roles still get asked. Confirm the parsed roster in one line.
4. **Offer to save** the answer as a standing preference, so it appears as the recommended option at the next handoff. It still gets asked.

Session 1's own research sub-agents do not wait for this question. They use the environment's preset Researcher, or whatever the user has asked for in the conversation.

## Checking availability

- Verify each choice against the environment's model catalog, aliases, or agent controls. Account, plan, or admin restrictions can make a model unavailable.
- If a chosen model or effort is unavailable, tell the user and offer the nearest alternative. Never substitute silently.
- If the environment cannot select a model per sub-agent, sub-agents inherit the session model. Record that limitation in the prompt.
- If per-agent *effort* is not selectable but the model is, say so. The effort then follows the session or agent-definition default.
- If sub-agents are unavailable entirely, Session 2 runs sequentially. Its handoff must say that review was not independent.

## Keeping review independent

Review is only useful if the reviewer does not share the authors' blind spots.

- Reviewers never review code they wrote, and always start from a fresh context.
- A Reviewer from a different model family than the Implementers adds independence when the environment offers one. Mention it as an option; do not force it.
- When Reviewer and Implementer share a model family, keep reviewers read-only, give each one a distinct lens, and keep the author's rationale out of the reviewer's brief. Reviewers should judge the code, not the author's explanation of it.

## Presets

Presets name a model **family** and its settings rather than a version number, because version numbers go stale within months and families don't. At handoff, resolve each "latest" entry to the newest matching model the environment actually offers, and put that exact identifier in the question and the compiled prompt. If it is unclear which model is newest, or a family is no longer offered, say so and let the user pick. A version is pinned only where the user deliberately chose one (Cursor's Researcher).

| Environment | Implementer | Researcher | Reviewer |
| --- | --- | --- | --- |
| Claude Code | `opus` · medium | `opus` · low | `opus` · medium |
| Codex | latest `astra` · high | latest `luna` · max | latest `sol` · medium |
| Cursor | latest Opus · medium | `grok-4.6` · high, fast | latest Opus · medium |
| Grok Build | latest Grok | latest Grok, fast where offered | latest Grok |

Why these defaults: a top-tier model at low effort is often cheaper than a mid-tier model and still stronger for exploration and synthesis. Hence `opus` · low as the Claude Code Researcher. Medium effort is enough for well-briefed implementation and review slices, because the Lead holds the hard cross-cutting reasoning.

Useful alternatives to list as options:

- **Researcher:** a small, fast model (such as `haiku` in Claude Code) for pure lookup and file-finding; a higher effort when research requires real synthesis across sources.
- **Reviewer:** a higher effort for high-risk changes (auth, money, data integrity, concurrency); a different model family where the environment offers one.
- **Implementer:** a higher effort for algorithmic or subtle concurrency work.

Claude Code notes:

- The aliases (`opus`, `sonnet`, `haiku`, and any others the environment exposes) already track the latest version of each family, so the compiled prompt can use them directly. Pin a full model ID only if the user asks.
- The Agent tool takes a per-call `model` override. Per-agent effort comes from an agent definition's frontmatter (`.claude/agents/<name>.md` with `model` and `effort`). If the chosen effort differs from the session effort and no suitable definition exists, Session 2 can create role definitions in the user's `~/.claude/agents/`. That is outside the repository, so it needs the user's approval.

For environments with no preset, offer the strongest agentic coding model as Implementer, a capable model at low effort or a fast model as Researcher, and a strong model as Reviewer, ideally from another family.

## What the compiled prompt must carry

- The environment.
- For Implementer, Researcher, and Reviewer: the exact model identifier, effort or other options, and native syntax where it differs.
- Any limitation found while checking availability, stated plainly.

Never leave "use the best model" for Session 2 to decide. The user already decided.
