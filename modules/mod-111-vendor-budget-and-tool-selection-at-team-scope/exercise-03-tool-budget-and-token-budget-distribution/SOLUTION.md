# Exercise 03 — Tool Budget and Token Budget Distribution

> Module: `mod-111-vendor-budget-and-tool-selection-at-team-scope`
> Exercise type: **design** (worked budget allocation + runbook + grading rubric)

## 1. Solution overview

The deliverable is a **team budget allocation sheet + enforcement runbook**
that a team manager can take to their next planning meeting and say: *"this
is how our monthly AI tool and token envelope is distributed across seats,
projects, and models, these are the caps the gateway enforces, these are
the thresholds that page someone, and this is the review date."* It is the
artifact that stops the team's AI spend from being a surprise on the first
of the month.

Scope. The exercise covers **two** budgets that are frequently conflated:

- **Tool budget** — recurring per-seat software spend (vendor IDE copilot
  seats, hosted agent runtimes, observability platforms, admin tools).
  Largely fixed per month; priced in seats and tiers.
- **Token budget** — usage-based model spend (provider API bills, gateway
  pass-through costs, hosted-agent-runtime usage charges). Highly variable;
  priced in input/output tokens, tool calls, or wall-clock on hosted
  agents.

These behave differently and need different controls. A good allocation
treats them separately and then reconciles them to a single monthly
envelope.

A good budget plan is:

- **Reconcilable to a single envelope.** The sum of per-seat +
  per-project + headroom must match the team's quarterly budget line
  to the dollar.
- **Enforceable at the gateway**, not only reported after the fact. Hard
  caps that only exist on a slide deck are not caps.
- **Attributed.** Every dollar spent has a seat, a project, or a shared
  pool it belongs to. Un-attributed spend is the fastest path to the
  budget looking fine until it doesn't.
- **Tiered by model**, not by vendor. A small-model call is a different
  unit from a large-model call; the plan prices them separately.
- **Honest about variance.** The plan names a p50 and a p95 for each
  project, not a point estimate. Budget breaches at the p95 line are
  normal; the plan says what happens.
- **Reviewed monthly**, not quarterly. This spend line moves too fast for
  a quarterly review to be useful.

Design decisions this solution encodes:

1. **Separate the two budgets.** Pretending tool and token spend are one
   line hides the tool spend's rigidity and the token spend's variance.
2. **Price by model tier, not by provider.** The team wants substitution
   optionality (see Mod-111 Ex-01/02). The budget must be stated in model
   tiers, so swapping providers does not require a re-plan.
3. **Hard caps live on the gateway, not on the team manager's calendar.**
   A budget that depends on a human remembering to look at a dashboard is
   not enforced. Caps are set on the gateway, per-seat or per-project.
4. **Headroom is a line item, not a hope.** The plan explicitly reserves a
   headroom pool for the month; headroom that is unreserved will be spent.
5. **Over-run has a documented playbook** that does not require the team
   manager's approval to throttle, only to un-throttle.
6. **Cost per outcome, not cost per token, is the thing to report up.**
   The budget links to the productivity measurement (Mod-104); tokens are
   an input, outcomes are the metric the skip cares about.

## 2. Implementation — the worked budget plan

### 2.1 Budget inputs (what you need before you can allocate)

Before the plan can be built, the manager assembles:

1. **The team's monthly envelope.** From finance, in dollars, for the
   quarter. Illustrative baseline used in the worked example: **$12 000 /
   month** for a 4-IC + 1-manager team. Replace with your number.
2. **Per-seat vendor pricing for the chosen copilot stack.** From the
   Mod-111 Ex-01 ADR. Illustrative worked example: vendor IDE copilot at
   $X per IC per month, billed annually; hosted agent runtime at $Y per
   workspace per month; observability platform at $Z per IC per month.
   *Do not quote real vendor prices in this document; read them from your
   contract and fill the sheet.*
3. **Model tier prices for the providers you are routing to.** Priced per
   million tokens for input and output separately, per tier. Fetch from
   each provider's pricing page and record the retrieval date — prices
   move.
4. **Historical consumption** for at least the last 60 days from the LLM
   gateway, broken down by: seat (`sub` claim), project (`project_id`
   header), model tier, and input/output tokens.
5. **Known upcoming work.** New projects, planned agent flows, A/B
   experiments (see Mod-111 Ex-02). Each gets its own estimate before
   being added to the sheet.

### 2.2 Model tier definitions (the pricing layer)

Costs are tracked by *tier*, not by vendor model name. Tier definitions
are decided per team and recorded. The team's gateway is configured with
a mapping from tier name → concrete provider models; this mapping is the
only place tiers map to brands.

| Tier | Use-case | Example mapping notes |
|---|---|---|
| **T-small** | Autocomplete, classification, simple summarisation, tool-call routing. | Each provider's smallest "haiku" / "mini" / "flash" tier. |
| **T-mid** | Everyday coding chat; ticket drafting; most review-support tasks. | Each provider's mid tier. |
| **T-large** | Long-context analysis; agent planning steps; eval-generating. | Each provider's flagship tier. |
| **T-reasoning** | Explicit reasoning budget required (hard refactor, hard debug). | Each provider's reasoning / thinking tier, where offered. |

Cost per million tokens is pulled from the provider's pricing page on the
day the budget is written. Record the date next to the number — this
line rotates often. Every provider lists per-tier pricing publicly on
their respective pricing pages (e.g. `anthropic.com/pricing`,
`openai.com/api/pricing`, `cloud.google.com/vertex-ai/pricing`, etc.);
check the page, do not quote from memory.

### 2.3 Budget allocation (the sheet)

The worked example uses a **$12 000 / month** envelope. Replace the
dollar numbers with your team's; the shape is the point.

```markdown
# Team budget — 2026-11 → 2027-01

**Envelope:** $12 000 / month, approved for the quarter by <finance owner>.
**Owner:** <team manager>.
**Gateway:** `llm-gateway.corp`.
**Pricing-refresh date:** 2026-11-10.

## A. Tool budget — fixed per-seat and per-team

| Line item | Qty | Unit cost / month | Monthly |
|---|---|---|---|
| Vendor IDE copilot (business tier) | 5 seats | $A | $5A |
| Hosted agent runtime (observability platform) | 1 workspace | $B | $B |
| Trace / eval storage | 1 account | $C | $C |
| Admin / SSO add-on | 1 | $D | $D |
| **A. Tool total** | | | **$T_A** |

(Replace $A..$D with contracted numbers. Keep the arithmetic visible.)

## B. Token budget — variable, controlled at the gateway

Starting envelope for section B: **$12 000 − $T_A − $headroom**.

### B.1 Per-seat allocation

| Seat | Role | Monthly seat token envelope | Hard cap at gateway |
|---|---|---|---|
| IC-1 | senior IC, A/B lead | $500 | $750 |
| IC-2 | senior IC | $400 | $600 |
| IC-3 | mid IC, agent flow owner | $500 | $750 |
| IC-4 | mid IC | $400 | $600 |
| Manager | code review, planning | $200 | $300 |
| **Per-seat subtotal (target)** | | **$2 000** | **$3 000 ceiling** |

### B.2 Per-project (team-shared) allocation

Agent flows and automations consume tokens on team shared projects. These
have their own envelope so a single bad run does not consume an IC's
budget.

| Project | Owner | Monthly target | Hard cap | Notes |
|---|---|---|---|---|
| Jira triage agent (Mod-111 Ex-02 Flow A) | IC-3 | $300 | $500 | Expected ~N runs / day. |
| Incident timeline agent (Mod-111 Ex-02 Flow B) | IC-1 | $400 | $750 | Long-horizon; p95 is 5× p50; see §2.5. |
| Provider A/B experiment | IC-1 | $400 | $500 | 30-day timebox. |
| Internal docs enrichment (batch) | IC-4 | $200 | $300 | Runs nightly in CI. |
| Code review support (per-PR) | shared | $300 | $500 | Metered per PR; cap per PR = $5. |
| **Per-project subtotal (target)** | | **$1 600** | **$2 550 ceiling** |

### B.3 Shared headroom pool

Headroom for burst, exploration, and incident response. Un-used headroom
rolls forward *within the quarter* only.

| Line | Monthly |
|---|---|
| Headroom pool | $400 target, $750 ceiling |

### B.4 Reconciliation

Target monthly spend: `$T_A + $2 000 + $1 600 + $400`. Must equal the
envelope within $100. If not, re-allocate before signing off.
```

The sheet is kept in the repo as `docs/budget-2026-q4.md`. The manager
owns it; finance and the skip can read it; the gateway is configured
from it.

### 2.4 Gateway enforcement (how the caps actually bite)

The caps in §2.3 are only real if the gateway enforces them. The gateway
owns four things:

1. **Per-identity usage counters.** Each seat is identified by the `sub`
   claim on the gateway's JWT; each project is identified by a header
   (`X-Project-Id`) the client sets. Counters tick on every call: input
   tokens, output tokens, cost in dollars.
2. **Soft and hard caps.** Soft cap = target; hard cap = ceiling. Crossing
   the soft cap logs a warning event and posts to the team ops channel.
   Crossing the hard cap **denies further calls** on that counter until
   the reset time or a manager override.
3. **Per-model-tier price table.** The gateway converts tokens to dollars
   using the tier price table (§2.2). This price table is a single config
   file reviewed monthly on the same cadence as this budget.
4. **Per-tool-call authorisation** (via Mod-106 Ex-03). The gateway is the
   point at which a tool call can also be refused — not only priced.

Example gateway config (illustrative; real gateways differ):

```yaml
# gateway/budgets.yaml — reviewed monthly

model_tiers:
  t-small:
    anthropic/claude-small:    { in_per_mtok: $A1, out_per_mtok: $A2 }
    openai/mini:               { in_per_mtok: $O1, out_per_mtok: $O2 }
  t-mid:
    anthropic/claude-mid:      { in_per_mtok: $A3, out_per_mtok: $A4 }
    openai/mid:                { in_per_mtok: $O3, out_per_mtok: $O4 }
  t-large:
    anthropic/claude-large:    { in_per_mtok: $A5, out_per_mtok: $A6 }
    openai/large:              { in_per_mtok: $O5, out_per_mtok: $O6 }
  t-reasoning:
    anthropic/claude-thinking: { in_per_mtok: $A7, out_per_mtok: $A8 }
    openai/reasoning:          { in_per_mtok: $O7, out_per_mtok: $O8 }

seats:
  ic-1: { monthly_target_usd: 500, monthly_hard_cap_usd: 750 }
  ic-2: { monthly_target_usd: 400, monthly_hard_cap_usd: 600 }
  ic-3: { monthly_target_usd: 500, monthly_hard_cap_usd: 750 }
  ic-4: { monthly_target_usd: 400, monthly_hard_cap_usd: 600 }
  mgr:  { monthly_target_usd: 200, monthly_hard_cap_usd: 300 }

projects:
  jira-triage:        { monthly_target_usd: 300, monthly_hard_cap_usd: 500, per_run_cap_usd: 1.5 }
  incident-timeline:  { monthly_target_usd: 400, monthly_hard_cap_usd: 750, per_run_cap_usd: 10 }
  provider-ab:        { monthly_target_usd: 400, monthly_hard_cap_usd: 500, per_run_cap_usd: 2 }
  docs-enrich-batch:  { monthly_target_usd: 200, monthly_hard_cap_usd: 300, per_run_cap_usd: 0.5 }
  code-review-assist: { monthly_target_usd: 300, monthly_hard_cap_usd: 500, per_pr_cap_usd: 5 }

shared:
  headroom: { monthly_target_usd: 400, monthly_hard_cap_usd: 750 }

alerts:
  soft_cap_crossed:
    channel: "#team-platform-ops"
    severity: warn
  hard_cap_crossed:
    channel: "#team-platform-ops"
    severity: page_manager_only
  daily_burn_rate_abnormal:
    rule: "projected monthly > 120% of target by day 10"
    channel: "#team-platform-ops"
```

Notes:

- Per-run and per-PR caps are **first-line** defenses. They stop one bad
  agent loop (a long-horizon flow in a retry storm, a code-review agent
  looping over a 10 000-line PR) from exhausting the project cap.
- The `page_manager_only` severity on hard caps is intentional: the team
  manager is the only person who can authorise an override; ICs cannot
  silently lift the cap.
- The soft-cap warning is to the ops channel, not to the specific IC, to
  normalise the signal and avoid individual embarrassment.

### 2.5 Estimating per-project token spend (the honest p50 / p95)

Point estimates for agent spend are almost always wrong. The honest
approach:

1. Run the agent against ≥ 20 representative inputs in a spike.
2. Record *cost per run* (not per token) for each.
3. Report p50 and p95 per run.
4. Multiply p95 × expected runs / month for the **hard cap**; multiply p50
   × expected runs / month for the **target**.

Worked example for the incident timeline agent (Mod-111 Ex-02 Flow B):

- Spike size: 20 past incidents.
- Observed cost per run: p50 = $0.80; p95 = $4.20.
- Expected monthly volume: ~100 incidents (illustrative; use incident
  tool's export).
- **Target** = 100 × $0.80 = $80. **Headroom to p95** = 100 × $4.20 = $420.
- **Hard cap** = $750 (> p95, covers one-bad-month and some growth).
- **Per-run cap** = $10 (> p95; prevents a single runaway run).

### 2.6 Monthly rollup and upward report

What gets reported upward each month (ties to Mod-104 productivity
measurement):

| Metric | Why |
|---|---|
| **Envelope vs. actual (tool + token, separately)** | Obvious. |
| **% of tokens at each model tier** | Lets the skip see substitution happening; drift to large-tier without a project cause is a warning. |
| **Per-project cost per outcome** (per ticket triaged, per retro drafted, per PR reviewed) | The number the skip actually cares about. |
| **Caps hit** (count by project, by seat) | Signal that targets are off, not that ICs are over. |
| **Headroom used** (dollars and reason) | Normalises use of the pool. |
| **Model routing changes** (gateway config diffs) | Evidence of tier substitution, not just spending growth. |

The report is one page; a worked template is in §2.7.

### 2.7 Monthly rollup example (fictional)

```markdown
# Team AI budget — 2026-11 rollup

**Envelope:** $12 000.
**Actual:** $11 420 (95%).
**Tool spend (A):** $T_A (fixed). On-plan.
**Token spend (B):** $11 420 − $T_A. 97% of target.

## By project (token spend)

| Project | Target | Actual | Caps hit | Cost per outcome |
|---|---|---|---|---|
| Jira triage agent | $300 | $285 | 0 | $0.42 / ticket |
| Incident timeline agent | $400 | $380 | 1 (one bad run) | $3.80 / retro |
| Provider A/B experiment | $400 | $420 | 1 (headroom used) | n/a |
| Internal docs enrichment | $200 | $190 | 0 | $0.03 / doc |
| Code review assistant | $300 | $340 | 1 (headroom used) | $0.62 / PR |

## Tier mix

| Tier | % of tokens | Notes |
|---|---|---|
| T-small | 48% | ↑ from 36% last month — routing improvements landed 2026-10-27. |
| T-mid | 34% | ↓ |
| T-large | 15% | Steady. |
| T-reasoning | 3% | Up from 1% — hard-refactor project this month. |

## Caps crossed

- 2026-11-07 — `incident-timeline` per-run cap on run `inc-4821`. Root
  cause: retry loop on a provider 5xx. Fix landed `incident-timeline#334`.
- 2026-11-18 — `provider-ab` soft cap crossed. Expected; the A/B pulls
  from the headroom pool by policy.
- 2026-11-24 — `code-review-assist` soft cap crossed. Driver: unusually
  large PR on the migration branch. No action; this is why there is a
  per-PR cap.

## Cost per outcome vs. last month

- Jira triage $0.48 → $0.42 / ticket. Improvement.
- Incident timeline $4.10 → $3.80 / retro. Within noise.
- Code review $0.55 → $0.62 / PR. Up; driver is longer PRs this cycle.

## Next month

- Nothing structural to change. Pricing refresh date set for 2026-12-05.
- A/B experiment concludes 2026-11-30; freed budget rolls into
  Flow B until the ADR is re-reviewed.
```

## 3. Validation steps

Before the plan is active:

1. **Does `tool + per-seat + per-project + headroom` sum to the
   envelope ± $100?** If not, do not sign off.
2. **Are hard caps configured on the gateway and visible in a
   dashboard?** A cap that only exists in the sheet is not a cap.
3. **Is each project's cap ≥ its p95 × expected runs?** If not, the plan
   will deny legitimate work; lower the target or raise the cap.
4. **Does each project have a per-run (or per-unit) cap smaller than its
   monthly cap?** Per-run caps are the first line of defense; the plan
   must set them explicitly.
5. **Does the alert routing distinguish soft-cap and hard-cap events?**
   Pagers on soft caps burn out the ops channel; silent hard caps hide
   problems.
6. **Does the monthly rollup capture cost per outcome, not only cost
   per token?** The upward report is the thing the skip reads; tokens
   are not an outcome.
7. **Is the pricing-refresh date recorded and in the gateway config?**
   Prices rotate. A stale price table turns every budget conversation
   into a reconciliation exercise.
8. **Is BYOK accounted for?** If any IC still pays with a personal key
   (common early) that spend is invisible to the plan. Either route it
   through the gateway or document the exception; do not pretend it
   does not exist.

## 4. Rubric

Use this to grade a submitted budget plan + runbook (self-assessment or
peer review). Each criterion scores 0–3. Pass bar: **18 / 27**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Tool vs. token spend separated** | One bucket | Mentioned as two | Separated on the sheet | Separated with distinct controls, variance stories, and rollup lines |
| R2 | **Reconciled to the envelope** | Does not reconcile | Reconciles approximately | Reconciles to the dollar | Reconciles + headroom explicit + variance named |
| R3 | **Priced by model tier, not vendor** | Priced per vendor | Mixed | Tiered, with one vendor mapped | Tier definitions + multi-vendor mapping + pricing-refresh date |
| R4 | **Hard caps set on the gateway** | None | Soft caps only | Hard caps set | Hard caps + per-run / per-unit caps + alert routing differentiated |
| R5 | **Per-project estimates use p50 / p95** | Point estimate | Mentioned | p50 or p95 only | p50 + p95 from a real spike, attached |
| R6 | **Attribution complete** | Spend floats | Some un-attributed | All attributed | All attributed + BYOK exceptions named with sunset date |
| R7 | **Headroom is a line item** | Absent | Mentioned informally | Named target | Target + ceiling + roll-forward rule |
| R8 | **Over-run runbook** | None | Prose | Playbook with named escalation | Playbook + gateway-enforced default throttle + manager-only un-throttle |
| R9 | **Upward report drives cost-per-outcome** | Tokens only | Mixed | Outcomes mentioned | Per-project cost per outcome + tier mix + caps-hit summary, all on one page |

## 5. Common mistakes

- **Reporting tokens upward.** The skip does not care about tokens; they
  care about whether the money moves work. Report cost per outcome
  (ticket, retro, PR). Tokens are an input, not a result.
- **No per-run caps.** The team configures a $500 project cap and leaves
  a long-horizon agent with no per-run ceiling. The first retry storm
  burns the whole month's project budget in 30 minutes. Per-run caps
  are the first line of defense.
- **Soft caps that page people.** The ops channel burns out and real
  hard-cap alerts get muted. Pager-level signal is for hard caps only.
- **Treating tool and token spend as one bucket.** Tool spend is rigid;
  token spend is highly variable. Blending them hides both signals.
- **Pricing in vendor names instead of tiers.** The budget breaks the
  moment the team swaps provider. Price in tiers; map tiers to brands
  in one config file.
- **Headroom that is not a line item.** Un-reserved headroom will be
  spent before the 10th of the month, and the manager will discover it
  on the 15th.
- **No pricing-refresh date.** Provider prices rotate; a stale table
  causes every budget conversation to become a forensic reconciliation.
- **BYOK leakage.** One IC quietly keeps a personal key because the
  gateway was slow last month. Their spend is invisible to the plan.
  Route through the gateway or record the exception.
- **Soft cap on provider A/B projects.** A/Bs are supposed to burn
  through headroom — that is their job. The plan should pay from the
  headroom pool by policy, not treat the burn as a surprise.
- **Treating caps hit as bad news.** A month with zero caps hit means
  the caps are set too high. A month with many caps hit on the same
  project means the targets are set too low. Caps are information; the
  rollup should report them, not hide them.
- **Promoting an agent to prod with a p50 estimate and no p95.** The
  first bad run of the month exceeds the whole project target by itself.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework)
  — the **Govern** function's expectations around **resourcing,
  accountability, and third-party management** are the structural
  reason this plan names an owner, a review cadence, hard caps, and an
  escalation path for over-runs. The **Measure** function's attention
  to *analysable, traceable* usage underwrites the gateway as the
  single source of truth for per-seat / per-project consumption.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/)
  — the supply-chain and model-availability risk categories motivate
  tiering by model and keeping the tier → brand mapping in one place:
  a budget plan that assumes one provider will always be available at
  one price is a plan that breaks on the first provider incident.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat taxonomy.
  Cited here because the per-run and per-PR caps also function as
  containment on *abuse* (prompt-driven retry storms, tool-call
  amplification); the budget enforcement is one layer of defense the
  team has against those threat classes, not only a finance control.

<!-- follow-up: once the completeness pipeline has shipped a second cycle
of module solutions, add a worked quarterly re-plan showing how the
monthly rollups drive the next quarter's envelope negotiation with
finance. -->
