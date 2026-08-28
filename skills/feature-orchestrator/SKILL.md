---
name: feature-orchestrator
description: Guide an iterative product and technical discussion, then turn the settled feature into a copy-ready orchestration prompt for a separate Codex implementation session. Use when the user wants to explore, narrow, summarize, or hand off a substantial feature without implementing it in the current session.
---

# Feature Orchestrator

Run Session 1 of a two-session feature workflow. Help the user think until the product and technical game plan feel settled; only then compile the implementation prompt for Session 2.

Do not rush from the first idea to the prompt. The conversation itself is part of the work.

## Boundary

- Do not implement the feature in Session 1.
- Inspect repository instructions, code, tests, dependencies, and relevant documentation read-only by default.
- Do not modify source, configuration, dependencies, schemas, or tests. A reversible workflow action such as creating a local branch is allowed only when the user explicitly asks for that action.
- Do not produce a separate authoritative implementation plan for Session 1 approval. Detailed technical reasoning, likely APIs, data models, flows, risks, candidate milestones, and workstream ideas are allowed when they help settle the feature or make the Session 2 prompt executable. Session 2 must still inspect the current repository and create its own actual plan.
- Do not silently resolve a product ambiguity that would materially alter behavior or scope. Ask a focused question when discussion, inspection, or research cannot resolve it safely.
- Return discussion, summaries, and prompts in the conversation unless the user asks to save them.

## Work conversationally

### Explore

Answer the user's current question at its natural altitude. Explain feasibility, product implications, technical options, complexity, and tradeoffs without forcing the entire workflow into every response.

Establish progressively:

- what the product does today;
- who the feature is for and the outcome they need;
- the intended user journey;
- what is in scope now versus later;
- the stack and architecture that fit the current repository;
- failure behavior, permissions, race conditions, data ownership, cost, and operability where material.

Separate observed repository facts, current external facts, user decisions, recommendations, and assumptions.

### Research when it changes a decision

- Delegate independent research, documentation checks, and codebase exploration to GPT-5.6 Terra at Extra High reasoning (`model: gpt-5.6-terra`, `reasoning_effort: xhigh`).
- Use as many Terra agents as are genuinely useful within available concurrency; give each one a bounded question and concrete deliverable.
- Prefer repository evidence and primary, current sources. Preserve direct links for unstable external claims.
- Synthesize the findings into the conversation. Do not paste raw research memos or perform research merely to demonstrate a swarm.

### Refine with the user

Expect the user to narrow scope, reject complexity, change priorities, or ask whether a proposed direction is correct. Reconcile each correction with earlier decisions rather than starting over or defending the previous recommendation.

Maintain a decision ledger mentally throughout the conversation:

- accepted product behavior;
- rejected or deferred scope;
- selected technologies and important exclusions;
- settled technical invariants;
- unresolved questions;
- requested verification and delivery expectations.

Do not expose the ledger mechanically after every turn. Use it to keep later answers and the final prompt consistent.

### Summarize on request

When the user asks for an overview, game plan, rundown, or recap, give a comprehensive current synthesis. For a complex feature, include the relevant stack, user experience, data model, workflows, security/authorization boundaries, failure recovery, race protection, testing, and sensible delivery order. Keep recommendations revisable until the user settles them.

This summary may be technically detailed; it is still not Session 2's authoritative implementation plan.

## Compile only when asked

Create the Session 2 prompt only when the user explicitly asks for the high-quality implementation/orchestration prompt or clearly declares the game plan settled.

Before drafting it, read [references/execution-prompt-contract.md](references/execution-prompt-contract.md) completely. Adapt its coverage to the feature. Preserve hard-won domain decisions in specific language; do not compress them into generic “follow best practices” instructions.

The prompt must preserve these workflow choices:

- The main Session 2 agent runs GPT-5.6 Sol at Extra High reasoning (`model: gpt-5.6-sol`, `reasoning_effort: xhigh`) and is the orchestrator, integration owner, and final verifier.
- Session 2 inspects the fresh repository, creates its own working plan, and then implements without stopping after the plan.
- Research, current-documentation work, codebase exploration, and independent read-only review use GPT-5.6 Terra sub-agents at Extra High reasoning (`model: gpt-5.6-terra`, `reasoning_effort: xhigh`).
- Every code change and review remediation uses GPT-5.6 Sol sub-agents at Extra High reasoning (`model: gpt-5.6-sol`, `reasoning_effort: xhigh`).
- The orchestrator decides what to parallelize or stagger from dependencies, shared files, risk, and available concurrency.
- There is no arbitrary agent cap, but every sub-agent needs bounded ownership, a concrete deliverable, dependencies, and a verification condition. Do not create a swarm without useful independent work.
- Implementation agents know they share the repository, preserve user changes, avoid conflicting ownership, and do not revert one another.
- Focused verification happens throughout implementation; whole-feature verification happens before independent review.
- Terra reviewers cover distinct material risks. The orchestrator triages their evidence, assigns validated fixes to Sol agents, and reruns affected checks.
- Run no more than two full independent review/remediation cycles. Handoff remaining disagreement or uncertainty instead of looping forever.
- Session 2 prepares a concrete, evidence-backed PR proposal but does not push branches or open pull requests without explicit approval.
- Completion ends with a handoff and the exact permission question using the actual proposed PR count.

## Delivery and PR thinking

When the settled feature benefits from multiple PRs, the prompt may include a provisional, outcome-oriented PR strategy and known dependencies. It must require Session 2 to validate and refine that strategy after seeing the actual implementation surface.

- Optimize for coherent, reviewable changes rather than an arbitrary PR count.
- Keep tests with the behavior they verify; do not postpone all tests to a final PR.
- Distinguish parallel workstreams from final PR boundaries. They are not necessarily the same.
- Preserve separability during implementation where practical, but do not force an unsafe split.

## Output when compiling

Return a short introduction followed by one copy-ready Session 2 prompt. Do not repeat the entire specification outside the prompt unless unresolved assumptions need the user's attention.

If a material choice is unresolved, ask for it before saying the prompt is ready. Do not disguise unresolved choices as instructions.

## Final self-check

Confirm that:

- Session 1 did not implement the feature or pretend its suggestions were Session 2's final plan;
- the prompt reflects every later correction and scope reduction from the conversation;
- repository facts, decisions, recommendations, and assumptions are not conflated;
- the prompt contains specific product behavior, technical invariants, and proof requirements—not generic advice;
- exact model and reasoning roles are correct: the main agent is Sol with `xhigh`; Terra with `xhigh` researches, explores, and reviews; Sol with `xhigh` edits and fixes;
- Session 2 owns planning, sequencing, integration, and final verification;
- the review loop is independent, evidence-based, and bounded;
- no sentence grants permission to push or open PRs before the final handoff question.
