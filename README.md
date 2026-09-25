# Astra Advisor

**GPT-6 Astra plans the work, chooses useful bounded delegation dynamically, and
owns verification and acceptance.**

Astra Advisor is a Codex plugin for capability-routed software delivery. Give Astra
the goal, constraints, and repository context; it decides whether independent work
should run alongside the parent session and chooses a supported native subagent
model and effort for each bounded deliverable.

## Cloud limitation

ChatGPT Work cloud `create_thread` must omit `model` and
`thinking`, so it cannot currently promise arbitrary model or effort control. Astra
does not dispatch a model-pinned request there by default. Native Codex subagents are usable
where the current tool schema exposes the needed controls.

## Go deeper

I write [Attention Heads](https://attentionheads.substack.com/) — deep,
evidence-backed writing on AI, cognition, and agentic engineering. The **Agentic
Engineering Field Notes** series covers the craft of using AI. [Subscribe](https://attentionheads.substack.com/subscribe?utm_source=github&utm_medium=readme&utm_campaign=astra-advisor)
to get new posts in your inbox.

## Quick start

Install the plugin in a current Codex CLI or ChatGPT desktop app with plugins
enabled. Start a fresh task after installation and select GPT-6 Astra at any effort
supported by the current Codex host:

~~~sh
codex plugin marketplace add DannyMac180/astra-advisor --ref main
codex plugin add astra-advisor@astra-advisor
~~~

Start a task with:

~~~text
Use $astra-advisor:orchestration to plan, build, verify, and review this work.
~~~

## How routing works

Astra remains the architect and acceptance owner in the primary GPT-6 Astra session
at the effort selected by the user. After capability preflight, Astra records the
parent model and effort as observed or unobservable before implementation or
delegation begins. The skill never changes the parent session.

When delegation helps, Astra uses the exposed generic `collaboration.spawn_agent`
tool with an explicit `model`, `reasoning_effort`, and `fork_turns: none`. It chooses
between `gpt-5.6-sol` and `gpt-5.6-luna` based on the task's risk,
context, and independent work. There are no predefined role TOMLs, companion
installer, role-to-model mapping, or fixed subagent count cap. Astra gives each
subagent a concrete bounded deliverable and continues useful parent work while it
runs.

These identifiers intentionally follow the models currently exposed by the native
subagent catalog. Astra must not request `gpt-6-sol` or `gpt-6-luna` until live tool
metadata exposes them; when it does, the same selection policy can adopt them without
silently substituting an unavailable model. GPT-6 pricing is recorded separately so
future receipts can use verified rates without rewriting historical snapshots.

Routing is outcome-first and cost-aware. Astra delegates only when parallelism,
specialist attention, or fresh-context review is likely to repay coordination cost.
It starts with the least costly model and effort that can reliably meet the bounded
acceptance criteria, uses Luna for clear low-risk execution and Sol when consequence,
ambiguity, or reasoning depth warrants it, and escalates only from task evidence or
measured evals. Live metadata and current official model guidance override this
heuristic.

Capability and safety decisions should be checked against the current official GPT-6
system card and model pages; delegation behavior should be checked against current
official Codex subagent and prompting documentation. The checked-in effort table and
routing heuristic are fallbacks, never stronger evidence than current official docs,
live tool schemas, or workload-specific evals.

The adaptive loop also incorporates the experimental framing proposed in the Codex
community discussion [“Beyond Auto mode: learning to allocate models, tools, and
subagents”](https://github.com/openai/codex/discussions/46658): keep requested and
observed allocation separate, reassess from independently verified task-state
changes, diagnose the failure location before escalating, and count routing,
handoffs, retries, and verification when evaluating efficiency. This discussion is a
design input, not official product documentation or proof that adaptive routing wins.

Live tool metadata is authoritative. The current documented effort snapshot is:

| Model | Known efforts |
| --- | --- |
| `gpt-5.6-sol` | `low`, `medium`, `high`, `xhigh`, `max`, `ultra` |
| `gpt-5.6-luna` | `low`, `medium`, `high`, `xhigh`, `max` |

If a selected model, effort, control, or tool is unavailable, conflicting, or
unobservable, Astra fails that delegation closed and reports the limitation. It does
not silently substitute a model, effort, role, or fabricated tool. Chosen values and
runtime-confirmed values are reported separately.

For substantial implementation, Astra inspects the complete diff and reruns the
requested checks, then sends the accumulated change set to a fresh read-only
reviewer. The reviewer can be either supported model at a live-supported
effort. Astra accepts the work only after the reviewer returns `ship`; `fix-first`
requires a new parent verification and fresh review, while `rethink` requires a
revised plan.

## Live visibility and cost receipts (0.2.0)

Every delegation announces its name, bounded task, selected model and reasoning
effort, and selection reason. Its result reports actual status and runtime-observed
settings, or explicitly says those settings are unobservable. These updates also
cover fresh reviewers. A requested setting is not proof of the realized setting.

Every task ends with an API-equivalent cost receipt. When native tools expose token
usage, the receipt estimates its USD price using the versioned snapshot and compares
that same token workload repriced entirely at Astra. It separates whole-task,
delegated-only, and partial coverage. Missing parent or reviewer usage prevents a
whole-task claim. Without observed usage, the receipt says why it is unavailable.

The difference is a **same-token API price comparison**. It does not measure what an
all-Astra run would actually consume, actual net task savings, quality, speed, or a
change to ChatGPT subscription charges or usage credits. No subagents means no
delegation savings. Reasoning effort does not multiply the token price.

Plugin 0.3.0 uses the current [pricing snapshot](plugins/astra-advisor/pricing/2026-09-25.json),
which records official source URLs and standard short-context USD rates per million
tokens. Each current entry records its own verification date; GPT-6 Sol and Luna were
verified September 25, while the carried-forward GPT-5.6 rates retain their September
4 verification date. The snapshot-level `verified_on` is therefore conservatively
the oldest included verification date. The immutable
[September 4 snapshot](plugins/astra-advisor/pricing/2026-09-04.json) remains available
for receipts produced by plugin 0.2.0. These are historical estimates; GPT-5.6 Sol
pricing is promotional and may change. The calculator rejects unsupported
long-context, service-tier, and cache-write cases instead of assuming standard rates.
It conservatively supports at most 128,000 input tokens per call; this is an
implementation support boundary, not a claimed official pricing threshold.

Try the clearly labeled illustrative workload (not a receipt for your task):

~~~sh
python3 plugins/astra-advisor/scripts/cost_receipt.py plugins/astra-advisor/examples/illustrative-usage.json
sh plugins/astra-advisor/scripts/verify.sh
~~~

The calculator emits JSON and accepts `--pricing PATH` for another verified snapshot.
Its input lists agents and unique atomic calls, usage provenance, coverage assertions,
and explicit pricing eligibility. It validates cached-input and reasoning-output
subsets, refuses overlapping aggregates, and keeps unknown usage separate from zero.
See the [operations reference](plugins/astra-advisor/skills/orchestration/references/operations.md)
for the input contract and receipt policy.

## ChatGPT app tasks

Separate app tasks require an explicit user request. For an explicit Codex app task,
`mcp__codex_app__create_thread` supports `model` and `thinking`; call
`mcp__codex_app__list_projects` first for project targets, use a worktree by default
for Git projects, and use local otherwise. Cloud `create_thread` omits both controls,
so the bounded limitation above applies. Do not use an API key, nested CLI, or
invented tool as a workaround.

## Updating

~~~sh
codex plugin marketplace upgrade astra-advisor
codex plugin add astra-advisor@astra-advisor
~~~

For local development, install this checkout as a marketplace:

~~~sh
cd /absolute/path/to/astra-advisor
codex plugin marketplace add /absolute/path/to/astra-advisor
codex plugin add astra-advisor@astra-advisor
~~~

For operational details, read
[the orchestration operations reference](plugins/astra-advisor/skills/orchestration/references/operations.md).
