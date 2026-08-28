# Runtime routing

Read this reference before the first sub-agent delegation and again before compiling the Session 2 prompt if routing has not already been resolved.

## Stable roles

- **Lead:** the required primary model for Session 2 and the model for orchestration, implementation, integration, code fixes, and final verification.
- **Researcher:** the model for external research, current documentation, read-only repository exploration, test-gap analysis, and independent review. During review it acts as the challenger, but keep the role name Researcher throughout the workflow.

The workflow depends on these responsibilities, not on a particular vendor's model names.

## Resolution order

Resolve routing once for the current session and reuse it unless the user changes the environment or model preference:

1. Honor an explicit user-supplied Lead or Researcher model and runtime setting exactly.
2. Detect the current agent development environment or harness and apply its preferred preset below when those models and options are actually selectable.
3. Verify availability through the environment's model catalog, configuration, documented aliases, or exposed agent controls. Account, plan, administrator, or provider restrictions may make a preset unavailable.
4. For an environment without a preset, select the strongest authorized agentic coding and reasoning model as Lead. Select the strongest sufficiently capable complementary model as Researcher, preferring a distinct model or provider when that improves independent scrutiny.
5. If only one suitable model is available, use it for both roles. Preserve useful independence with separate fresh contexts, bounded task prompts, read-only reviewer permissions, and reviewers that did not author the changes.
6. If per-agent model selection is unavailable, inherit the model chosen by the environment and record the limitation.
7. If sub-agent delegation is unavailable, use the strongest available sequential workflow. Do not pretend that a same-context review was independent; disclose the degraded review in the final handoff.

Do not interrupt the product discussion merely to recite a routine preset. Ask the user only when the environment cannot be identified, multiple materially different choices remain, or applying a fallback would violate an explicit preference. When reporting or compiling the prompt, use the environment's exact native model identifiers and option syntax.

A skill cannot assume that it can switch the parent session's model. If Session 1 is already running on a different model, do not claim to have changed it. Continue the product discussion, resolve the intended Lead for Session 2, and encode that requirement in the compiled prompt.

## Preferred presets

These are preferences, not fictional guarantees. Translate the settings into the active environment's native syntax and fall back honestly when a model or option is unavailable.

| Environment | Lead | Researcher |
| --- | --- | --- |
| Codex | `gpt-5.6-sol` with `reasoning_effort: xhigh` | `gpt-5.6-terra` with `reasoning_effort: xhigh` |
| Claude Code | `opus` with `effort: high` | `sonnet` with `effort: high` |
| Cursor | `gpt-5.6-sol[effort=xhigh]` | `grok-4.6[effort=high,fast=true]` |
| Grok Build | `grok-4.6` | `grok-4.6` |

Claude Code's `opus` and `sonnet` aliases are intentional so the preset follows the environment's current Opus and Sonnet generations instead of pinning a version that immediately becomes stale. Pin a full model identifier only when the user explicitly requests it.

For Grok Build, the preset uses its native Grok 4.6 model for both roles. If the user has configured custom models and explicitly prefers one, honor that override through the normal resolution order.

## Requirements for the compiled prompt

The Session 2 prompt must contain:

- the resolved environment or harness;
- the exact Lead model identifier and runtime options;
- the exact Researcher model identifier and runtime options;
- a concise fallback note when the preferred preset could not be enforced;
- role-based instructions so every implementation and remediation goes to Lead agents and every research or independent review task goes to Researcher agents.

Never leave generic phrases such as “use the best model” in the final prompt. Runtime resolution happens in Session 1 so Session 2 can execute deterministically.
