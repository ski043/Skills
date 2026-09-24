# Feature Orchestrator: Session 2 execution prompt contract

Read this reference only when the user has settled the game plan and asks for the implementation prompt.

The prompt must be self-contained enough for a fresh implementation session to reconstruct the intended outcome without the prior conversation. It is an execution contract, not Session 1's binding implementation plan: Session 2 still inspects the current repository, creates its working plan, chooses the agent topology, and adapts to what it finds.

## Calibrate depth to the feature

Do not force every prompt into the same length. Preserve the decisions that would otherwise be rediscovered or implemented incorrectly.

For a substantial cross-cutting feature, consider dedicated sections for the relevant subset of:

- repository and expected branch;
- objective and product scope;
- mandatory repository or framework instructions;
- current architecture and integration points;
- explicit exclusions and deferred phases;
- UI behavior and lifecycle states;
- stack and dependency constraints;
- data model and ownership;
- public and internal APIs;
- background, ingestion, or generation workflows;
- authorization, tenancy, and untrusted inputs;
- concurrency, races, idempotency, and retries;
- quotas, rate limits, cost, and observability;
- deletion and failure recovery;
- tests, evaluations, runtime verification, and definition of done;
- provisional workstreams, milestones, and PR strategy;
- final handoff and external-action boundary.

Omit irrelevant sections. Do not replace feature-specific conclusions with vague language such as “handle errors” or “follow best practices.”

## Recommended prompt frame

```markdown
You are the primary implementation agent responsible for delivering [complete feature outcome].

Sub-agent model roster (chosen by the user; use exactly as written):
  environment: [resolved ADE or harness]
  Implementer:  [model] · [effort/options]
  Researcher:   [model] · [effort/options]
  Reviewer:     [model] · [effort/options]

[Only if needed: one line per known limitation, e.g. per-agent effort not selectable, so sub-agents inherit session effort. Never substitute a model silently.]

You are the Lead and run on the model this session was launched with. Use as many sub-agents of each role as the work supports.

Repository:
  [absolute repository path]

Expected branch or starting git state:
  [branch/state, or tell the agent to inspect and preserve it]

Do not stop after producing a plan. Read the repository instructions, inspect the current code and state, plan, implement, verify, get an independent review, fix validated findings, and continue until the work is genuinely complete or a material blocker needs the user. Routine decisions are yours; make them and record them.

[State relevant authorization boundaries, especially production, destructive actions, secrets, deployments, purchases, and external writes.]

## Orchestration

You are the orchestrator, integration owner, and final verifier. You choose the decomposition, sequencing, and integration strategy after inspecting the repository.

Role routing:

- **Implementer** sub-agents (roster model and effort) make delegated code changes, write the tests for those changes, and apply accepted review fixes.
- **Researcher** sub-agents do docs and API verification, read-only exploration, and test-gap analysis. They are cheap and fast, so use them freely and in parallel instead of spending your own context on searches.
- **Reviewer** sub-agents do the independent review after verification (see below).
- You implement directly when a change is small or too tightly coupled to split. Delegation buys parallelism and a clean context; it is not a rule.
- Express roster settings in the environment's native syntax, and do not emit options it lacks. If per-agent model or effort selection is unavailable, use what the environment allows and note it in the handoff.

Topology:

- Parallelize independent work. Stagger dependent work. Never give two agents simultaneous ownership of the same file.
- Use as many agents as the work genuinely supports. One agent is better than a swarm when the work is sequential or tightly coupled.
- Keep cross-workstream decisions, conflict resolution, and whole-feature verification with yourself.

Briefing sub-agents. They start with none of your context, so a brief should read like a handoff to a strong engineer who just joined:

- the goal and why it matters to the feature;
- the relevant settled decisions and invariants from this prompt, quoted rather than paraphrased when precision matters;
- owned files or modules, and the interfaces they must honor with neighboring work;
- the done-condition and the focused checks to run before reporting;
- the report shape: what changed, check results, and anything surprising or unresolved;
- for Implementers: they share the repository with other agents and the user, must preserve changes they did not make, and must never revert someone else's work.

Progress file. Keep a concise living file outside the source tree (or in an ignored scratch location) with the plan, workstream status, decisions and deviations, and verification evidence. Update it at each milestone. It is what lets you resume accurately after context compaction, and it becomes the backbone of the final handoff.

## Build with your eyes open

[Include for any feature with a UI, front-end, or browser-observable behavior. Omit for pure backend or library work.]

The environment's built-in browser (or whatever browser automation it provides) is your vision. Use it the way a front-end engineer uses a second monitor: as part of writing the code, not as a checkpoint afterwards. Types, lint, and tests cannot show you layout, spacing, state transitions, or an interaction that feels wrong. Only looking does.

- Get the app running early through the repository's documented dev server, and reuse one that is already running. Keep a browser tab on the screen you are building.
- Work in a tight loop: write a small piece, look at it, interact with it, then fix and look again. Don't batch up a whole screen of changes before the first look.
- Look at the states as you build them, not at the end: empty, loading, error, permission-denied, long or edge-case content, narrow widths, and dark mode where the product supports it. Watch the console and network while you do.
- When something surprises you, trust what you see over what the code implies, and find out why.
- This applies to every agent that touches UI. Implementers build their slice in the same loop and report what they saw, not just what they changed. The Lead drives the integrated flows end to end. Reviewers with a UX or failure-recovery lens may use the browser read-only.
- Keep a few screenshots or concise observations of key flows for the handoff. They are a by-product of working this way, not a separate step.
- Do not sign in to real third-party accounts, submit real payments, or trigger external writes from the browser without explicit authorization.

## Mandatory preparation

- Read every applicable repository-owned instruction before editing, including `AGENTS.md`, `CLAUDE.md`, or environment-specific rules where present.
- Read version-specific framework guidance required by those instructions.
- Inspect the branch, worktrees, status, current dependencies, existing tests, and relevant code before planning.
- Preserve pre-existing user changes and avoid unrelated refactors.
- Verify unfamiliar or version-sensitive APIs against installed types or current primary documentation instead of guessing.
- Use applicable installed skills where they materially improve the task.

## Existing product and architecture

[Summarize observed current behavior, architecture, important integration points, and navigational file paths. Paths help the agent start inspection; they are not a mandatory edit list.]

## Product scope

### Required outcome

- [Concrete user-visible behavior]

### Explicit exclusions

- [Rejected, deferred, or later-phase scope]

### Behavioral and failure contract

- [Observable success behavior]
- [Empty, loading, retry, interruption, concurrency, fallback, or human-handoff behavior where relevant]

## Technical requirements and invariants

[Use feature-specific subsections. Record selected stack, data ownership, workflows, APIs, security boundaries, lifecycle states, race protection, idempotency, deletion, quotas, and operational requirements at the depth already settled in Session 1.]

Distinguish firm requirements from suggested defaults that Session 2 may adjust when repository evidence requires it. Require an explanation for every material deviation.

## Verification requirements

- Add or update tests and evaluations alongside the behavior they prove.
- Require each workstream to run focused checks before handoff to the orchestrator.
- After integration, run the relevant repository-level typecheck, lint, tests, build, and runtime/product verification.
- Exercise meaningful negative, authorization, concurrency, and recovery paths—not only the happy path.
- Use mocks or fakes for paid or external services unless the user has explicitly authorized real calls.
- Verify every acceptance criterion with observable evidence and retain commands, scenarios, and results for the final handoff.
- Do not claim completion based only on compilation.

## Independent review and remediation

After implementation and whole-feature verification pass:

1. Launch **Reviewer** sub-agents (roster model and effort). They are fresh, read-only, and did not author the changes. Give each one a distinct lens chosen from the actual risks, for example spec coverage, framework/API correctness, authorization and tenant isolation, races and idempotency, failure recovery in the UI, or test adequacy. Give them the spec and the diff, not the implementers' rationale.
2. Reviewers cite concrete files, lines, and reproduction steps, and rank findings by severity.
3. Confirm each finding before acting on it. Reproduce it, trace it in the code, or have a Researcher check it. Reviewers produce false positives, and fixing a non-bug adds risk. Dismiss unconfirmed findings with the evidence.
4. Hand confirmed fixes to Implementer sub-agents with a focused brief, then rerun the affected checks.
5. Run at most two complete review/remediation cycles. Surface any remaining disagreement or uncertainty in the handoff instead of looping.

External AI review tools come later. They do not replace tests, runtime verification, or human judgment.

## Delivery and provisional PR strategy

[Insert the candidate outcome-oriented PR boundaries and known dependencies developed in Session 1, if useful. State clearly that Session 2 must validate and refine them after understanding the actual change graph.]

- Keep prospective PRs coherent and reviewable, with tests accompanying the behavior they verify.
- Distinguish agent workstreams from PR boundaries.
- Preserve separable change groups where practical, but report when the implementation makes a proposed split unsafe or misleading.
- Local branches, worktrees, or commits may be used when they are already authorized or needed for safe implementation isolation. Do not forbid these reversible local mechanics merely because external PR creation is gated.
- Do not push branches or open pull requests. Those external actions require explicit approval after the final handoff.

## Definition of done

- [Feature-specific observable outcome]
- [Negative/fallback outcome]
- [Security or ownership invariant]
- [Verification and regression expectation]
- No unauthorized production deployment, external mutation, or committed secret occurred.

## Required final handoff

When implementation and verification are complete, report:

1. What was implemented and the resulting behavior.
2. Material architecture decisions and deviations from this prompt.
3. Sub-agent workstreams and the roster actually used, including any deviations or limitations.
4. Changed files/modules organized by prospective PR.
5. Tests, checks, evaluation cases, and runtime verification with results, including browser evidence for UI flows.
6. Independent review findings: accepted/fixed, dismissed with evidence, and unresolved risks.
7. Required environment variables, migrations, manual setup, and known limitations.
8. Active branch/worktree/commit state and confirmation that nothing was pushed or opened without approval.
9. A concrete proposed PR set: actual count, title, purpose, contents, validation, dependencies, and order for each PR.
10. The areas the user should inspect most carefully.

The final line, only when implementation is complete and PR-ready, must be:

> Implementation and verification are complete. I recommend creating **[actual N] pull requests** in the order above. Should I create, push, and open these pull requests now?
```

## Quality check before returning the prompt

- It reflects the latest settled scope, including every later correction and exclusion.
- It carries enough concrete product and technical context to avoid rediscovery, without presenting Session 1's suggestions as the final code plan.
- The roster block lists Implementer, Researcher, and Reviewer with exact identifiers and options, plus any honest limitation. No model choice is left open.
- It tells Session 2 to plan and then keep going, choose its own topology, brief sub-agents with full context, and keep a progress file.
- Features with a UI include "Build with your eyes open", so Session 2 uses the browser continuously while writing code, not as a final check.
- Verification precedes review, findings are confirmed before they are fixed, and there are at most two review cycles.
- A provisional PR strategy appears only where useful, and the actual count is left to Session 2. Local branches, worktrees, and commits stay allowed; pushing and opening PRs are gated.
- No placeholders remain. For `[actual N]`, instruct Session 2 to insert its real count. Never invent it in Session 1.
