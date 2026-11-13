# Exercise 02 — Agent Framework Selection Matrix

> Module: `mod-111-vendor-budget-and-tool-selection-at-team-scope`
> Exercise type: **design** (worked decision matrix + rationale + grading rubric)

## 1. Solution overview

The deliverable is a one-page **agent framework selection matrix** that a
team manager can take to their architecture review and say: *"this is the
framework we are standardising on for team-authored agents, these are the
criteria that made us pick it, these are the use-cases it is explicitly
not for, and this is the review date at which we will re-open the
decision."* It is the artifact that stops the team's agent code from
splintering into five half-working frameworks across three projects.

"Agent framework" in this exercise is the broad category: any library or
hosted runtime that an engineer uses to compose an LLM loop with tools,
state, and a control flow — ranging from a thin loop on top of a provider
SDK to a full hosted agent service with a visual graph editor. The matrix
should be able to score options including (illustrative, not exhaustive):

- Thin provider SDKs with a hand-rolled loop (the Anthropic SDK / OpenAI
  SDK / Google Gen AI SDK with user-written agent code).
- Provider-shipped agent SDKs (e.g. Anthropic's agent SDK, OpenAI's agent
  tooling, Google's agent tooling).
- Open-source orchestration libraries (e.g. LangGraph / LangChain, DSPy,
  LlamaIndex agents, CrewAI, AutoGen, Haystack).
- Hosted agent runtimes from cloud providers (Amazon Bedrock Agents,
  Vertex AI Agent Builder, Azure AI Agent Service), subject to the team's
  cloud posture.

A good matrix is:

- **Scoped by use-case.** A team rarely needs one framework for everything.
  The honest answer is often two: a thin framework for single-call or short
  tool-use flows, and a heavier framework for long-horizon, graph-shaped
  flows. The matrix must make that explicit.
- **Weighted against the team's actual constraints**, not a generic feature
  grid. Compliance, model-switch optionality, and observability outweigh
  "has agents in the name" for most real teams.
- **Mapped to a reviewable artifact** — an ADR committed to the repo, with
  an owner, an evaluation window, a review date, and a named kill-switch.
- **Honest about the operational cost**. "Library you can install" and
  "service you must on-call" are not the same row. The matrix prices on-call
  burden explicitly.

Design decisions this solution encodes:

1. **Score the loop shape before the brand.** "Agent" covers at least three
   loop shapes: one-shot tool use, bounded multi-step tool use (3–10 calls),
   and long-horizon graph-shaped flows with branching and checkpoints. The
   matrix scores by *what the team is actually building*; most teams need
   different answers per shape.
2. **Model-agnosticism is a weighted criterion, not a nice-to-have.** A
   framework bound to one provider is a lock-in cost. The matrix prices it.
3. **Observability is table-stakes.** A framework whose only observability
   is "print statements" is unsuitable for production agents. The matrix
   demands evidence of tracing, run inspection, eval hook-in, and audit
   logging — not a logo on a slide.
4. **Human-in-the-loop (HITL) surface is a kill-switch, not a weight, for
   high-effect agents.** If the framework cannot express a per-tool
   confirmation gate the team can enforce, it is disqualified for any flow
   that performs mutating or externally-visible actions. Low-effect flows
   can live without it.
5. **"Thin loop on provider SDK" is a first-class row.** For many teams the
   right answer *is* ~200 lines of their own code on top of the provider
   SDK. The matrix must score this honestly, not treat it as a straw man.
6. **The exit path is a scored criterion.** How costly is it to move a flow
   off the chosen framework? Framework choice decisions are the kind of
   choice a team regrets at year two, not month two.
7. **Governance maps to Mod-110 / Mod-106.** Framework choice directly
   affects whether tool authorisation (Mod-106 Ex-03), delegation contracts
   (Mod-106 Ex-02), and data/IP handling (Mod-110 Ex-04) are enforceable.

## 2. Implementation — the worked matrix and ADR

### 2.1 Decision options (the rows)

Every row is scored for a specific *loop shape*. Rows that are a poor fit
for a shape are not simply "0" — they are marked **N/A** so a bad score on
one shape does not drag down an otherwise sensible row.

| Row | Shape fit | Example |
|---|---|---|
| **F1 — Thin loop on provider SDK** | One-shot + bounded multi-step | ~200 LOC team-owned loop on top of the Anthropic SDK or OpenAI SDK; tools declared in-tree. |
| **F2 — Provider-shipped agent SDK** | Bounded multi-step; some long-horizon | Provider's own agent SDK/runtime for their model (Anthropic's agent SDK, OpenAI's agent tooling, Google's agent tooling). |
| **F3 — OSS orchestration (graph-shaped)** | Long-horizon, graph-shaped | LangGraph; DSPy pipelines; custom graph on top of a state machine library. |
| **F4 — OSS orchestration (role / crew / conversation)** | Multi-agent conversations, role play | CrewAI, AutoGen, LangChain's agent executors. |
| **F5 — Hosted cloud agent runtime** | Bounded multi-step; some long-horizon | Amazon Bedrock Agents, Vertex AI Agent Builder, Azure AI Agent Service. |
| **F6 — MCP-first host as the "framework"** | Mostly dev-time / assistant-shaped flows | Claude Code / Cursor / Codex CLI used as the agent surface for internal workflows, with the team's MCP servers as the tool plane. |

### 2.2 Criteria (the columns)

Grouped into kill-switch criteria (gate before scoring) and weighted
criteria. Weights are defaults; teams re-weight before looking at any
framework.

**Kill-switch group (pass / fail) — only applied to flows that actually
need the capability.**

| ID | Kill-switch criterion | Pass means |
|---|---|---|
| K1 | **HITL per-tool-call gate is expressible** | For any mutating or externally-visible tool, the framework exposes a hook where a human can approve the specific tool call with its arguments before it executes. |
| K2 | **Deterministic bound on tool loop** | Max step count, max wall-clock, max spend, max tool-call rate are first-class and enforceable. |
| K3 | **No training on the team's traces (hosted rows only)** | For F2 / F5 / F6, the hosted runtime's terms contractually commit that prompts, completions, and traces are not used to train models. |
| K4 | **Model-provider optionality** | Either the framework is model-agnostic across the providers the team needs, or the team has written confirmation that a single-provider lock-in is acceptable for *this* flow. |
| K5 | **Observability is non-optional** | Tracing / run inspection is built-in or a first-class add-on; the team can export runs for off-line eval and audit. |

**Weighted group.** Score 0–3 per cell. Default weights are illustrative;
re-weight per team.

| ID | Criterion | Default weight | What 3 looks like | What 0 looks like |
|---|---|---|---|---|
| W1 | **Fit to loop shape** | 15 | Framework is idiomatic for the shape; team can read the flow as code without a tutorial. | Framework forces a shape mismatch; flow becomes hostile to read or extend. |
| W2 | **Observability depth** | 12 | Structured traces per step; input/output/latency/tokens/cost per call; export for eval; sensible UI. | `print` and nothing else. |
| W3 | **Tool / MCP extensibility** | 10 | Open plugin / MCP surface; team can publish and revoke tools; per-tool authorisation is a hook, not a fork. | Closed; new tools need framework patches. |
| W4 | **Governance hooks** | 10 | Admin surface for per-flow quotas; audit log per run; policy enforcement at the tool-call layer; per-environment isolation. | None. |
| W5 | **Cost predictability** | 8 | Deterministic bounds (see K2) plus flat or metered-with-hard-cap pricing. Cost per run estimable before launch. | Metered without hard cap; one bad branch runs a bill. |
| W6 | **Model-switch optionality** | 10 | Model-agnostic or single-line switch; no provider-specific prompt scaffolding leaking into the business logic. | Bound to one provider's output schema with no abstraction. |
| W7 | **Operational burden** | 8 | Library the team pip-installs; no on-call for the framework itself. Or hosted runtime with an SLA the team is willing to accept. | Self-hosted long-running service the team must on-call. |
| W8 | **Incident response support** | 7 | Named maintainer / vendor security contact; incident history; vulnerability disclosure history. | No security contact; CVE history uninspectable. |
| W9 | **Cost to leave** | 8 | Flow logic is portable; tool definitions are not framework-captured; prompt templates are plain files. | Flow is a graph in the vendor's private schema; leaving means a rewrite. |
| W10 | **Supply-chain posture** | 7 | SBOM / signed releases; transitive dependency review feasible; security-sensitive components have named maintainers. | Dependency tree of unknown provenance. |
| W11 | **Eval / test ergonomics** | 7 | Reproducible runs from recorded traces; replay in CI; eval harness hook-ins; deterministic mode available. | Agents only exist at runtime; no reproducible fixtures. |
| W12 | **Team enablement cost** | 5 | Onboarding is < 1 day; docs cover the team's workflow; concepts match team vocabulary. | Week per IC; constant terminology mismatch. |

Max weighted score: `3 × (15+12+10+10+8+10+8+7+8+7+7+5) = 3 × 107 = 321`.

### 2.3 The scoring protocol

1. **Enumerate the team's flows and group by shape.** One matrix per *shape*;
   do not try to score a single framework as "best overall" when the team's
   flows span one-shot and long-horizon.
2. **Weight before you score.** Fill the weight column from the team's
   obligations and constraints *before* opening any framework. Record a
   one-line rationale per changed weight.
3. **Run kill-switches first.** Mark rows that fail — do not score them.
4. **Score survivors 0–3.** Every cell has a one-line citation: a working
   spike, a doc link, a benchmark run, a GitHub issue. Vibes are not a
   citation.
5. **Run a one- to two-week spike for the top two rows.** The spike builds
   one real team flow against each framework and reports (a) a diff of
   actual lines of team code, (b) an observability screenshot, and (c) the
   per-run cost and latency.
6. **Sensitivity check the top three weights.** Flip each by 20%; note any
   winner changes in the ADR.
7. **Pick per shape. Commit the ADR.** The ADR names: the row chosen per
   shape, the top two reasons per pick, the kill-switches struck, the
   review date, the named owner, the kill-switch conditions that would end
   the standard mid-cycle, and the explicit list of flows for which the
   standard is *not* required.

### 2.4 Worked example — "Platform API team" (fictional)

Team context (continuing the example from exercise-01):
- 4 ICs, infrastructure team. Code in Go + TypeScript.
- Team-authored agent flows in 2026-Q4:
  - **Flow A — Jira triage agent** (one-shot + bounded multi-step; reads a
    ticket, enriches with internal docs via MCP, proposes a priority and a
    draft comment, HITL approves before posting).
  - **Flow B — Incident timeline agent** (long-horizon, graph-shaped; given
    an incident ID, walks logs, tickets, Slack, and open PRs across up to
    40 steps; produces a draft retrospective).
- Governance: all outbound posts require HITL (see Mod-106 Ex-04).
- Data posture: internal docs touched are classified *restricted*; model
  traces must stay out of any training pipeline (see Mod-110 Ex-04).
- Model posture: team wants to run a provider A/B on both flows within
  90 days (see Mod-111 Ex-03 for budgeting).

Per-shape matrices follow. Weights are the same across both shapes because
the team's underlying obligations do not change.

Changed weights (both shapes):
- `W4 Governance hooks: 10 → 12` (HITL and audit are hard requirements).
- `W6 Model-switch optionality: 10 → 12` (A/B commitment).
- `W2 Observability depth: 12 → 14` (incident flow needs replay).
- Others at defaults. Total weight: 111.

#### Flow A — one-shot + bounded multi-step

Kill-switch pass:

| Row | K1 | K2 | K3 | K4 | K5 | Survives? |
|---|---|---|---|---|---|---|
| F1 Thin loop on provider SDK | ✅ | ✅ | n/a (not hosted) | ✅ | ✅ (via OpenTelemetry) | ✅ |
| F2 Provider-shipped agent SDK | ✅ | ✅ | ✅ | depends on provider; pass this cycle under BYOK | ✅ | ✅ |
| F3 OSS orchestration (graph) | ✅ | ✅ | n/a | ✅ | ✅ | ✅ |
| F4 OSS orchestration (roles) | depends on library | ✅ | n/a | ✅ | partial | partial — risk documented |
| F5 Hosted cloud agent runtime | ✅ | ✅ | <!-- pending-evidence: confirm against the specific cloud runtime's current terms for the team's region --> | depends | ✅ | deferred |
| F6 MCP-first host (Claude Code etc.) | ✅ | ✅ | ✅ (business tier) | ✅ via gateway | ✅ | ✅ (but shape-mismatched — see W1) |

Weighted scoring (illustrative; replace with evidence from the spike):

| Criterion | W | F1 | F2 | F3 | F6 |
|---|---|---|---|---|---|
| W1 Fit to loop shape | 15 | 3 | 3 | 2 | 1 |
| W2 Observability depth | 14 | 2 | 3 | 3 | 2 |
| W3 Tool / MCP extensibility | 10 | 2 | 3 | 3 | 3 |
| W4 Governance hooks | 12 | 2 | 3 | 3 | 3 |
| W5 Cost predictability | 8 | 3 | 3 | 2 | 3 |
| W6 Model-switch optionality | 12 | 3 | 1 | 3 | 2 |
| W7 Operational burden | 8 | 3 | 3 | 2 | 3 |
| W8 Incident response support | 7 | 2 | 3 | 2 | 3 |
| W9 Cost to leave | 8 | 3 | 1 | 2 | 2 |
| W10 Supply-chain posture | 7 | 3 | 3 | 2 | 3 |
| W11 Eval / test ergonomics | 7 | 3 | 2 | 3 | 1 |
| W12 Team enablement cost | 5 | 3 | 2 | 2 | 3 |
| **Total (W × score)** | | **283** | **263** | **255** | **245** |

Decision for Flow A: **F1 — thin loop on provider SDK**, with tool/MCP
wiring the team already owns.

#### Flow B — long-horizon, graph-shaped

Kill-switch pass: (same table; F4 remains partial, F5 deferred.)

Weighted scoring (illustrative):

| Criterion | W | F1 | F2 | F3 |
|---|---|---|---|---|
| W1 Fit to loop shape | 15 | 1 | 2 | 3 |
| W2 Observability depth | 14 | 1 | 3 | 3 |
| W3 Tool / MCP extensibility | 10 | 2 | 3 | 3 |
| W4 Governance hooks | 12 | 2 | 3 | 3 |
| W5 Cost predictability | 8 | 1 | 3 | 3 |
| W6 Model-switch optionality | 12 | 3 | 1 | 3 |
| W7 Operational burden | 8 | 3 | 3 | 2 |
| W8 Incident response support | 7 | 2 | 3 | 2 |
| W9 Cost to leave | 8 | 3 | 1 | 2 |
| W10 Supply-chain posture | 7 | 3 | 3 | 2 |
| W11 Eval / test ergonomics | 7 | 1 | 2 | 3 |
| W12 Team enablement cost | 5 | 2 | 2 | 2 |
| **Total (W × score)** | | **195** | **251** | **278** |

Decision for Flow B: **F3 — OSS orchestration (graph)**.

Sensitivity check: dropping `W6 Model-switch optionality` back to 10 moves
F3 to 254 and F2 to 249; F3 still wins. Dropping `W2 Observability depth`
back to 12 moves F3 to 258 and F2 to 245; still F3. Not sensitive.

### 2.5 The ADR (what gets committed)

```markdown
# ADR-2026-11-02 — Team standard agent frameworks

**Status:** Accepted, 2026-11-13.
**Owner:** <team manager>.
**Review date:** 2027-02-13 (90 days). Off-cycle review triggered by:
major release or deprecation on the chosen framework; provider terms
change breaking K3 or K4; any agent-related incident on a team flow.

## Decision

The team standardises on **two** frameworks, scoped by flow shape:

- **F1 — Thin loop on the provider SDK** — default for one-shot and
  bounded multi-step flows (≤ ~10 tool calls, no branching complexity).
- **F3 — OSS orchestration (graph-shaped)** — for long-horizon flows
  with branching, checkpointing, or persistent state across sessions.

The standard is binding for net-new team flows. Existing flows are
exempt until they cross the review bar in §Consequences.

## Context

Decision follows Mod-111 Ex-02's matrix. Winners score 283 (F1 for Flow A)
and 278 (F3 for Flow B) against weights the team set in advance.
Sensitivity checks on the top three weights did not change the winner.
Spike repos: `platform-api/agent-spike-f1`, `platform-api/agent-spike-f3`.
Full scoring with citations: `docs/agent-framework-matrix.md`.

## Consequences

- New flows of either shape use the corresponding standard. An agent
  flow that does not fit either standard is a signal to re-open this
  ADR, not to silently add a third framework.
- All team flows, regardless of framework, go through the team's LLM
  gateway (see exercise-01 ADR). Framework-level BYOK into providers is
  not allowed off-gateway.
- HITL gates for all mutating or externally-visible tools, per
  Mod-106 Ex-03. The ADR does not accept "the framework does not make
  this convenient" as a reason to skip a gate.
- Observability: OpenTelemetry traces exported to the team's trace
  store; per-run eval harness ingestion.
- Existing flows that span frameworks have a 90-day migration window if
  and only if they already meet all the kill-switches on the standard.

## Kill-switch conditions

The standard is reversed, not re-scored, if:
1. Provider terms break K3 (training opt-out) or K4 (switch optionality).
2. The chosen framework deprecates a load-bearing primitive (graph, tool
   binding, HITL hook) on a timeline shorter than 6 months.
3. An agent-related incident on a team flow traces to a framework defect
   without an acceptable fix window.

## Alternatives considered

- **F2 — provider-shipped agent SDK.** Close second on both shapes;
  rejected on W6 and W9 while the team is mid-A/B. May be revisited if
  the team concludes the A/B.
- **F4 — OSS orchestration (role / crew).** Shape mismatch for both
  flows; useful when the team has a real multi-agent-conversation case.
- **F5 — hosted cloud agent runtime.** Deferred until K3 is confirmed in
  writing for the team's region; see `<!-- pending-evidence: ... -->` in
  the matrix doc.
- **F6 — MCP-first host as the framework.** Great for dev-time
  assistant-shaped flows (and is the exercise-01 answer there); shape
  mismatch for backend agent flows that must run headless in CI.

## References

- `docs/agent-framework-matrix.md` — full scoring with citations.
- Mod-111 Ex-01 — copilot build-vs-buy ADR.
- Mod-111 Ex-04 — vendor due diligence packet.
- Mod-111 Ex-05 — DPA / SOC 2 posture review.
- Mod-110 Ex-04 — data and IP handling policy.
- Mod-106 Ex-02 — `AGENTS.md` delegation contract.
- Mod-106 Ex-03 — MCP tool authorisation policy.
- Mod-106 Ex-04 — HITL checkpoint design.
```

### 2.6 Why "one framework for everything" is the wrong goal

A team that insists on one framework usually chooses by what reads well on
the busiest flow and then quietly tolerates the mismatch everywhere else.
The mismatch shows up as (a) one-shot flows wrapped in a graph that adds
no value, or (b) long-horizon flows crammed into a thin loop that quietly
grows into a bespoke framework of its own. Scoring per shape avoids both.

Standardising on *at most two* frameworks is the practical middle path. A
third framework in a team of < 20 ICs is almost always a sign the standard
has rotted and the ADR should be re-opened, not a sign the team needs a
third standard.

## 3. Validation steps

Before taking the matrix or ADR to the architecture review:

1. **Did you score per shape, not overall?** A single "best framework"
   number across heterogeneous flows is almost always wrong. Produce one
   matrix per shape or justify why one is enough.
2. **Did you run a spike?** Scoring "fit to loop shape" without having
   written at least a thin end-to-end version of a team flow in the
   candidate framework is the most common failure mode. The spike is the
   evidence for W1 and W11.
3. **Is K1 (HITL) scored against a real hook, not the framework's README?**
   "Supports human-in-the-loop" in docs is not the same as "the team can
   enforce a per-tool approval on tool X without a fork."
4. **Is K3 (no-training) pulled from written vendor terms for the specific
   tier and region, not from a general policy statement?** If the answer is
   not clear, mark `<!-- pending-evidence: ... -->` and do not score the row.
5. **Did you run a sensitivity check?** Flip each of the top three weights
   by 20%. If the winner changes, say so in the ADR.
6. **Does the ADR explicitly handle the "not for" cases?** A standard that
   does not name what it is *not* for is a standard that will quietly be
   violated for the first flow that does not fit.
7. **Is governance wired to Mod-106 / Mod-110?** Tool authorisation,
   delegation contract, data/IP policy must all still apply at the
   framework layer; the ADR must name the integration points.
8. **Does the ADR name a kill-switch, an owner, and a dated review?** Agent
   framework choice decays faster than most architecture decisions.

## 4. Rubric

Use this to grade a submitted matrix + ADR (self-assessment or peer
review). Each criterion scores 0–3. Pass bar: **18 / 27**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Shape-scoped scoring** | One matrix for "best framework" | Mentions shapes but scores overall | One matrix per shape with explicit N/A cells | One matrix per shape, with the team's flows actually mapped to shapes |
| R2 | **Rows cover the real option space** | One or two rows | Includes OSS and provider SDK | Includes thin-loop, OSS graph, provider SDK | Thin-loop, OSS graph, OSS roles, provider SDK, hosted runtime, MCP-first host — all considered |
| R3 | **Kill-switches present and enforced** | None | Listed but not enforced | K1–K5 scored, rows struck | K1–K5 scored against artefacts and rows struck; K3 pulled from written terms |
| R4 | **Weights justified against obligations** | Defaults, no rationale | Some rationale | Every changed weight has a one-sentence rationale | Rationale cites a specific obligation (policy, incident history, deadline) |
| R5 | **Spikes run on top rows** | None | Spike on one row | Spikes on top two rows | Spikes on top two rows with diff / observability / cost evidence attached |
| R6 | **Cells cite evidence** | Bare numbers | Some cells cite | Most cells cite | Every scored cell cites a doc / spike / benchmark / issue |
| R7 | **Sensitivity check performed** | Not done | Mentioned | On one weight | On top three weights; result's robustness stated in the ADR |
| R8 | **"Cost to leave" counted as a criterion** | Absent | Mentioned informally | Scored but not prominent | W9 weighted, scored with citation, mentioned in the ADR narrative |
| R9 | **ADR committed with owner, review, kill-switch** | None | Prose decision | ADR without review date | ADR with status / owner / review date / kill-switch / "not for" scope |

## 5. Common mistakes

- **Choosing by vibes from a demo video.** The demo shows the graph-view
  and the team buys without ever checking whether the framework's HITL
  hook is really a hook or just a callback the team cannot authorise
  against. Score K1 against a working spike or do not score it.
- **Confusing "has agents in the name" with "fits the team's loop shape."**
  Many things called "agent frameworks" are role-play conversation
  libraries; many things not called agent frameworks are perfectly good
  for the team's actual flow.
- **Letting the loudest IC pick the framework.** The IC who ran the first
  hackathon flow usually drives the de facto standard. That is not
  evidence, it is availability bias. The matrix is how the team votes
  with evidence instead.
- **Ignoring observability.** A flow that is not reproducible is a flow
  that cannot be post-mortemed. Agents without trace export are agents
  the team will regret shipping on the first incident.
- **Over-standardising.** Forcing a one-shot flow through a graph
  framework because "we are a LangGraph shop now" wastes everyone's time
  and makes the code worse.
- **Under-standardising.** The opposite: three frameworks across four
  flows because no-one made a decision. Every maintainer onboards three
  times.
- **No kill-switch on provider lock-in.** The team quietly grows
  provider-specific prompt scaffolding into the business logic and loses
  W6 and W9 without ever scoring them.
- **Pretending hosted runtimes have no data story.** F5 rows must be
  scored against the actual written terms for the tier and region, not
  against a cloud brand you already pay for.
- **Pricing frameworks only on library cost.** The real cost is
  operational: on-call, upgrades, trace storage, eval harness
  maintenance. Score W7 accordingly.
- **Omitting "what the standard is not for."** A standard without a
  stated non-scope will quietly be bent to fit every new idea. State the
  non-scope.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework)
  — the **Map** function's framing of *intended use and context* is why
  the matrix scores per loop shape rather than per brand. The **Measure**
  function's demand that AI risk be evaluated against *analysable,
  traceable* evidence is why K5 (observability) is a kill-switch and W2
  is heavily weighted. The **Manage** function's accountability
  expectations drive the ADR's named owner, dated review, and explicit
  kill-switch conditions.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/)
  — the supply-chain category motivates W10; the data-poisoning and
  model-extraction categories motivate K3 (no-training on team traces)
  and the specific attention to hosted-runtime terms.
- [MITRE ATLAS](https://atlas.mitre.org/) — the adversarial-threat
  taxonomy for ML systems. Used to sanity-check that the agent-specific
  threat surface (tool-call abuse, prompt-injection via tool output,
  indirect data exfiltration) is categorised against a shared vocabulary
  rather than invented locally. Cited directly in the per-tool
  authorisation policy (Mod-106 Ex-03) that this ADR assumes.

<!-- follow-up: after the team concludes the model-provider A/B referenced
in Flow B, add a 90-day re-review example showing how an A/B outcome
either confirms F3 or triggers a specific row's re-score. -->
