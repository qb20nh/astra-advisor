---
name: orchestration
description: "Plan, route, implement, verify, and review substantial work with GPT-6 Astra and dynamically selected native Codex subagents."
---

# Astra Advisor Orchestration

Act as the architect and acceptance owner. Keep the primary session on GPT-6 Astra
at the effort selected by the user. Astra owns intent, architecture, decomposition,
delegation decisions, parent verification, and acceptance. A skill cannot change the
parent model or effort, and must honor the invocation's effort. If observable runtime
metadata says the parent model is not `gpt-6-astra`, report the mismatch as a
selection prerequisite and do not claim Astra orchestration. If the model or effort
is unobservable, disclose that fact rather than inventing confirmation.

Optimize for the requested outcome, not orchestration activity. Establish the goal,
success criteria, constraints, available evidence, and required final artifact, then
use the fewest useful agent and tool loops that can satisfy them. Do not delegate
merely because a subagent is available. Prefer direct parent execution when work is
small, sequential, tightly coupled to parent context, or cheaper to complete than to
specify and integrate.

After capability preflight and before the first implementation or delegation task
call, emit a short, machine-auditable declaration:

~~~text
ASTRA ROUTE
parent: <observed model or unobservable> / <observed effort or unobservable>
delegation: <none or the selected native subagent models and efforts>
risk: <concise, task-specific rationale>
~~~

Report model and effort as observed evidence. If metadata does not expose a value,
say that it is unobservable; never claim a runtime pin that was not confirmed. Read
[the operations reference](references/operations.md) before the first delegation.

Use the generic `collaboration.spawn_agent` tool only when it is exposed by the
current tool schema. Each selected subagent must receive an explicit `model`, an
explicit supported `reasoning_effort`, and `fork_turns: none`. Choose dynamically
between `gpt-5.6-sol` and `gpt-5.6-luna` based on the task's risk,
context, and independent work available; do not encode a role-to-model mapping or a
fixed number of subagents. Give every subagent a concrete, bounded, independent
deliverable while Astra continues useful parent work. Do not duplicate the parent's
implementation or verification in a subagent.

These are the current catalog-exposed identifiers. Do not request `gpt-6-sol` or
`gpt-6-luna` until the live spawn schema or catalog exposes them. If availability
changes, prefer the live-supported replacement and report that evidence; never turn
a planned model migration into an unavailable request.

Start with the least costly model and effort that evidence indicates can reliably
satisfy the deliverable. Escalate only for a task-specific reason such as high
consequence, architectural ambiguity, difficult debugging, broad synthesis, or a
failed lower-cost attempt. Prefer `gpt-5.6-luna` for well-specified, low-risk work and
`gpt-5.6-sol` when the bounded task needs stronger reasoning or review. This is a
routing heuristic, not a capability guarantee: live metadata, current official model
guidance, and measured task evals override it. Never infer capability from a model
name or price alone.

Make each subagent prompt outcome-first and self-contained. Include only the owned
deliverable, relevant context and files, success criteria, constraints, expected
return, validation evidence, and a stop condition. Exclude duplicated background,
prescribed step-by-step reasoning, and unrelated repository context. Ask the agent to
return concise evidence rather than a narrative of its hidden reasoning.

Treat verification as the boundary for reassessment. Do not switch model, effort, or
tool merely because time elapsed, output looks uncertain, or an attempt failed. First
check whether the action reduced the unresolved task state and whether its acceptance
contract passed. Diagnose the likely failure location—selection, execution, context,
tool or environment, handoff/integration, verification, or task definition—then keep,
change, or stop the allocation. A stronger model is only one possible intervention.
Record requested settings separately from effective or observed settings.

Tools and their public schemas are authoritative. Select only an effort the current
tool exposes. If a selected model, effort, spawn control, or required native tool is
missing, conflicting, unavailable, or unobservable, fail that delegation closed and
continue only with safe parent work or report the limitation. Never silently
substitute a model, effort, role, or fabricated tool. Introspection may clarify an
omitted runtime field; it cannot replace an available public contract.

For a substantial implementation, Astra must inspect the complete diff and rerun the
requested checks before starting a fresh read-only review. The reviewer may be either
supported subagent model, selected dynamically with explicit model and effort
controls. Give it the actual change set and evidence, and require:

~~~text
ASTRA REVIEW
VERDICT: ship | fix-first | rethink
REASON: <evidence-based reason>
FINDINGS: <precise findings or none>
RESIDUAL RISK: <remaining risk or none>
~~~

Accept a substantial implementation only after the fresh reviewer returns `ship`.
After `fix-first`, the parent applies the correction, verifies again, and obtains a
new fresh review. A reviewer remains read-only and never fixes its own findings.

Use native Codex subagents in the ChatGPT app when the exposed interface supports the
needed controls. Separate app tasks require an explicit user request. For an explicit
Codex app task, `mcp__codex_app__create_thread` supports `model` and `thinking`; call
`mcp__codex_app__list_projects` first for project targets, using a worktree by default
for Git projects and local otherwise. ChatGPT Work cloud `create_thread` must omit
`model` and `thinking`, so it cannot currently promise arbitrary model or effort
control; do not dispatch a model-pinned request there by default or use an API-key/CLI
workaround. Use a future native work tool only when its schema exposes the required
controls.

## Live delegation and completion receipts

Automatically show a short user-visible lifecycle update for **every** delegation,
including reviews, before dispatch and on completion or failure. Before dispatch,
include task name, exact bounded ownership, requested model and effort, and the
reason for that selection. On return, include agent ID, actual status, and observed
model/effort with their evidence source; if unavailable say `unobservable`. If they
differ from the request, show both. A submitted request is not runtime confirmation.
Keep progress readable; report meaningful changes without polling narration.

At the completion of **every task**, even solo, failed, or blocked tasks, emit an
`API-EQUIVALENT COST RECEIPT` using the calculator described in the operations
reference, or a precise unavailable status when usage cannot be observed. Capture
available usage with source, unique call IDs, agent identity, and scope as work runs.
Include parent, implementers, and all reviewers before claiming whole-task coverage.
Do not invent token counts, missing rates, or success percentages. Unknown is not zero.

Distinguish observed tokens from pricing estimates and partial coverage. Show routed
USD and the same observed tokens repriced at Astra only when comparable; label the
difference a **same-token API price comparison**, never measured all-Astra behavior,
actual net task savings, subscription charges, or improved quality/speed. If parent
usage is missing, label any available delegated-only comparison separately. With no
subagents there are no delegation savings. Effort is metadata, not a price multiplier.
Use the versioned snapshot and disclose its date and promotional Sol pricing. Reject
unsupported pricing regimes rather than silently using standard rates. An illustrative
fixture is optional and must remain separate from this task's receipt.
