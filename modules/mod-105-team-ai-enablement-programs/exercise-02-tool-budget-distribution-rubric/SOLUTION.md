# Exercise 02 — The Tool-Budget Distribution Rubric

> Module: `mod-105-team-ai-enablement-programs`
> Exercise type: **design** (worked rubric + decision rationale + grading rubric)

## 1. Solution overview

A team manager running an AI enablement program will almost always face a fixed budget — licence seats, a monthly token/credit pool, or both — that is smaller than "one of everything for everyone." The default responses are (a) allocate evenly across ICs, which ignores that AI use is highly non-uniform in practice, or (b) allocate to the loudest voice, which optimises for lobbying rather than for the team's goals. Neither produces a defensible decision.

The deliverable is a **tool-budget distribution rubric**: a short, scored template the manager fills out *before* allocating licences or credits, and revisits every month to rebalance. It makes four things explicit that are usually fuzzy:

1. **What outcome is this budget buying?** (Fluency across the team, throughput on a specific workflow, capability on a specific risk tier.)
2. **What are the allocation axes?** (Role, project risk, experiment phase, historical use.)
3. **How is unused capacity reclaimed?**
4. **What is the review cadence and who makes the call?**

Design decisions this solution encodes:

1. **Budget is an enablement lever, not just a procurement decision.** Allocation shapes who learns what. A manager who gives the heaviest tooling only to the most senior ICs reinforces a fluency gap; a manager who spreads evenly dilutes learning signal. The rubric forces an explicit choice about which pattern the team wants.
2. **Allocate against outcomes, not seniority.** Two ICs doing the same review-heavy work have the same budget claim, regardless of level. Seniority can be a tiebreaker but is not the primary axis.
3. **Every seat is on probation.** Licences and credit pools must be rebalanced at least monthly. A seat that has been unused for 30 days is reclaimable *by default* — the holder opts back in, not out.
4. **The rubric is scored, not just filled.** A scored rubric produces a ranked allocation and lets the manager defend the decision when (not if) someone appeals it.
5. **Reserve capacity is budgeted first.** A fraction of the budget (default 15%) is held back for new hires, short-term experiments, and unexpected high-risk projects. Allocating 100% day one guarantees that the first new need triggers a political negotiation.

## 2. Worked implementation — the rubric

### 2.1 Rubric template

```markdown
# Tool-Budget Distribution Rubric — <team>, <month/quarter>

Owner: <manager name>
Budget pool: <N> seats of <tool>, <$X> or <T> tokens per month
Review cadence: monthly on the 1st; ad-hoc reclamation after 30 days
  of non-use

## A. Outcome the budget is buying

- **A1. Primary outcome.** (One sentence: "This budget is buying
  fluency across the whole team" OR "throughput on the
  billing-migration project" OR "capability on security-sensitive
  changes.")
- **A2. Non-goals.** Explicit: what this budget is NOT for.
- **A3. Reserve pool.** % of budget held back for new hires + ad-hoc
  needs. Default 15%.

## B. Allocation axes (fill out per IC)

For each IC, score 0–3 on each axis. The totals rank the allocation.

- **B1. Workflow fit.** How much of this IC's current workflow
  benefits from this specific tool? (0 = none, 3 = daily core
  workflow.)
- **B2. Risk-tier exposure.** Does this IC regularly touch
  high-risk changes (auth, billing, PII, customer comms)? Higher
  risk justifies more capable tooling. (0 = no, 3 = daily.)
- **B3. Current fluency and the *delta* a seat would unlock.** An
  IC already at ceiling with a lower-tier tool is a weaker claim
  than one blocked by lack of access. (0 = no delta, 3 = blocked
  without it.)
- **B4. Experiment role.** Is this IC part of the Phase 2 workflow
  experiment (see Exercise 01)? Experiment participants get a
  tiebreaker boost. (0 = no, 1 = tiebreaker boost.)

## C. Allocation output

| IC | B1 | B2 | B3 | B4 | Total | Rank | Allocation |
|---|---|---|---|---|---|---|---|
| … | … | … | … | … | … | … | … |

## D. Review rules

- **D1. Monthly rebalance.** On the 1st of each month, the manager
  re-scores. ICs with usage below <threshold> for two consecutive
  months lose their seat to the reserve pool.
- **D2. Reclamation.** A seat unused for 30 days is reclaimable by
  default; the holder is notified 7 days before.
- **D3. Appeals.** An IC may appeal their score in writing to the
  manager within 5 days of allocation. Written reasons are recorded
  alongside the rubric.
- **D4. Transparency.** Scores and allocations are visible to the
  team. The reserve pool and its current holders are visible too.

## E. Guardrails

- **E1. No single IC above <X>% of the budget** without manager +
  director sign-off. Prevents one power-user from draining the pool.
- **E2. Quarterly audit.** At the end of each quarter, compare
  allocation to actual outcomes. Scores that didn't predict usage
  are evidence the rubric needs tuning.
- **E3. Vendor change-in-cost response.** If per-seat or per-token
  cost changes by >20%, re-run the rubric within one week. See
  mod-111 vendor/budget module for the escalation path.
```

### 2.2 Worked example — a hypothetical six-IC team

To show the rubric in motion, consider a team of six ICs with a budget of **4 Pro-tier seats of an AI coding assistant and $600/month of API credits**. The manager has set a reserve of 15% ($90 of credits, zero reserved seats — seats aren't divisible in this example).

| IC        | Role / current work                                     | B1 workflow | B2 risk | B3 delta | B4 exp | Total | Rank | Allocation                                              |
|-----------|----------------------------------------------------------|-------------|---------|----------|--------|-------|------|---------------------------------------------------------|
| A (tech lead) | Code review-heavy, auth and billing areas             | 3           | 3       | 2        | 1      | 9     | 1    | Pro seat; $120 credits                                  |
| B         | Frontend features, customer-visible copy drafting        | 3           | 2       | 3        | 0      | 8     | 2    | Pro seat; $120 credits                                  |
| C         | On-call + reliability work; writes runbooks              | 2           | 3       | 3        | 1      | 9     | 1    | Pro seat; $90 credits                                   |
| D         | Backend features, moderate complexity                    | 2           | 1       | 3        | 0      | 6     | 4    | Pro seat (last one); $90 credits                        |
| E         | Data pipeline, occasional AI use                         | 1           | 1       | 2        | 0      | 4     | 5    | Lower-tier seat (outside Pro pool); $60 credits         |
| F         | Infra, infrequent assistant use, prefers manual work     | 1           | 2       | 0        | 0      | 3     | 6    | Lower-tier seat; $30 credits                            |
| Reserve   | New hire H1, workflow experiment overflow               | —           | —       | —        | —      | —     | —    | $90 credits reserved                                    |

Observations the rubric surfaces:

- The tech lead and the SRE tie on raw totals. In practice, both get a Pro seat — the 4 available seats are not the bottleneck. If seats were constrained, the tiebreaker rule (**B4** experiment role) would split them cleanly.
- IC D ranks 4th and gets the last Pro seat. The rubric surfaces that IC D's score is only marginally above IC E's; the manager should watch usage and be prepared to swap at the month-1 review.
- IC F scores lowest on *every* axis — they have told the manager they prefer manual workflows. The rubric honours that: F gets the lowest allocation, not none, so they have access when they want it.
- The reserve pool ($90) is what covers the incoming hire in two weeks without needing to renegotiate the pool.

Walk this allocation forward 30 days: suppose IC D used $5 of their $90 credits, and IC E used $70 of their $60 credits (over-spent, pulled from reserve). At the month-1 review, D's seat moves to probation; if D's usage remains flat through month 2, their Pro seat is reclaimed and swapped with E's lower-tier seat. The rubric made that decision defensible — the manager points to the scores, the usage data, and the pre-published reclamation rule.

### 2.3 Decision rationale (why these axes and not others)

- **Workflow fit (B1) is the primary axis, not seniority.** Two ICs doing the same review-heavy work have the same budget claim. Seniority as a primary axis entrenches a fluency gap — the senior IC gets the better tool, widens their lead, and the junior IC never gets the exposure needed to catch up. That's a recipe for the atrophy failure mode addressed in mod-107.
- **Risk-tier exposure (B2) weights the stakes.** An IC doing auth or billing work benefits disproportionately from a more capable assistant because the cost of an AI-assisted bug is much higher. This is the same logic that justifies the risk-tier overlay in the mod-103 review checklist (Exercise 01). The OWASP ML Top 10 security categories (ML01 Input Manipulation, ML05 Model Theft, ML06 AI Supply Chain Attacks) are a useful lens for scoring B2 upward on roles that touch those surfaces.
- **Fluency-delta (B3), not current fluency.** We score the *marginal benefit* of a seat, not the IC's current level. An IC already at ceiling with a free tier has a weaker claim than one blocked without a Pro seat.
- **Experiment role (B4) is a tiebreaker, not a primary axis.** Making experiment participation a large weight distorts incentives (ICs lobby to be on the experiment to get the tool). As a small tiebreaker, it just reflects that experiment participants need the tool more during the experiment window.
- **Reserve-first budgeting (A3).** Allocating 100% of the budget on day one guarantees the first new need triggers a political negotiation. A 15% reserve absorbs that need without reopening the allocation. 15% is the recommended default; it scales up (toward 25%) on teams with high hiring churn and down (toward 10%) on stable teams. No official source prescribes a reserve percentage for AI tool budgets at team scope — the 15% default is a working heuristic; validate against local finance / procurement practice before using it as a hard rule.
- **Explicit appeals and transparency (D3, D4).** Opaque allocation is the single most common cause of team-culture damage from these decisions. Scores and allocations visible to the whole team invert that: appeals are grounded in the rubric, not in private conversations.
- **Vendor-cost-change trigger (E3).** AI tool pricing is volatile; a per-seat cost change of 20%+ invalidates the budget assumption that drove the allocation. Re-running the rubric is cheaper than absorbing the surprise. Hand off to the mod-111 process for the vendor-side response.

## 3. Validation steps

1. **Dogfood on last quarter's allocation.** Score your team as it stood 90 days ago. Compare the rubric's ranking to the actual allocation and to what you *now* know about who used the tool and who didn't. If the rubric would have produced a materially better allocation, publish it. If not, the rubric either doesn't fit your team or is missing an axis.
2. **Dry-run on a hypothetical crunch.** Imagine the budget is cut 30% mid-quarter. Re-run the rubric against the smaller pool. If the resulting reclamations are plausible and defensible, the rubric works under pressure. If any reclamation looks absurd, tighten the axes.
3. **Appeals rehearsal.** Ask one IC to pick the ranking they would appeal and draft the appeal in writing. If the rubric cannot answer the appeal with its own scores, add the missing axis.
4. **Peer-review with one manager and one finance partner.** Managers catch team-dynamics issues; finance partners catch budget-math issues (tax, licence term, true-up mechanics).
5. **Pilot for one month.** Apply the rubric, run the month-1 review, look at reclamation actions. If reclamations were zero, the rubric is too generous or the usage threshold is too low. If reclamations were painful, the 30-day window may be too tight for this team.

Validation is behavioural: you are checking that the rubric produces a decision that survives contact with usage data and with team scrutiny.

## 4. Rubric for grading a submitted rubric

Use this to grade a submitted tool-budget distribution rubric (self-assessment or peer review). Each criterion scores 0–3.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Outcome framing (A1)** | Missing | "To buy some AI tooling" | One clear outcome | One clear outcome + explicit non-goals (A2) |
| R2 | **Reserve pool (A3)** | None | <5% | 10–20% with rationale | 10–20% with rationale and named reserve-holder |
| R3 | **Allocation axes** | Seniority-only | 1–2 axes, workflow and seniority | 3 axes including workflow, risk, delta | 4 axes: workflow, risk, delta, experiment-tiebreaker |
| R4 | **Scoring produces a ranked output** | No scoring | Qualitative only | Scored but not ranked | Scored, ranked, tie-break rule documented |
| R5 | **Review cadence (D1)** | None | "Occasionally" | Monthly review | Monthly review + explicit reclamation threshold |
| R6 | **Reclamation rules (D2)** | None | Discretionary | 30-day inactivity reclamation | 30-day reclamation + 7-day notice + opt-back-in path |
| R7 | **Appeals (D3)** | None | Verbal only | Written appeals, 5-day window | Written + visible appeal record + rubric updated on appeal patterns |
| R8 | **Transparency (D4)** | Private | Shared on request | Allocations visible to team | Scores + allocations + reserve + usage visible |
| R9 | **Guardrails (E1–E3)** | None | One guardrail | 2–3 guardrails | 3 guardrails including vendor-cost-change trigger and quarterly audit |
| R10 | **Worked example present** | None | Table only | Table + commentary | Table + commentary + month-1 review walk-through |

**Pass bar: 20/30.** Below that, iterate before publishing to the team.

## 5. Common mistakes

- **Allocating by seniority first.** Entrenches fluency gaps and signals to the team that AI tools are a perk, not an enablement lever.
- **Allocating evenly "to be fair."** Even allocation ignores that AI use is highly non-uniform. It produces under-tooled power-users and over-tooled infrequent users at the same time.
- **100%-on-day-one allocation.** Guarantees the next new hire or emergency project triggers a political reallocation. Reserve pools are cheap.
- **No reclamation.** A seat held for 90 days but used for 2 is a dead asset. The reclamation rule must be automatic and pre-published, not a case-by-case conversation.
- **Opaque allocation.** "I decided who gets what and I won't show my reasoning" is the single most common culture-damaging move in this exercise. Transparency is the antidote.
- **Scoring without usage data on the review cycle.** Scores without measured usage become a self-justifying cycle. Monthly usage audit is what turns the rubric into a learning loop.
- **Ignoring the vendor-price change trigger.** Teams get surprised by 30% per-seat price increases when they should be re-running the rubric automatically at the 20% threshold.
- **Treating the rubric as permanent.** The axes that matter change as the tool portfolio changes. Expect to revise the rubric itself every quarter, not just the allocation it produces.
- **Confusing this rubric with a performance review.** B1–B4 score *fit*, not *IC performance*. A low score means "this seat would be wasted on this IC's current work," not "this IC is weak." Mis-reading this collapses the whole rubric into a status ranking.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the **Govern** function's requirements for documented resource-allocation decisions and the **Map** function's call for understanding where AI tools are applied are both relevant to the rubric's transparency and audit items.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — the security-exposure framing (B2 risk-tier axis) uses the ML Top 10 categories as a lens for which workloads justify higher-tier tooling.
- [MITRE ATLAS](https://atlas.mitre.org/) — used to validate that risk-tier scoring covers the adversarial-use surfaces most likely to apply to assistant-authored code.

<!-- follow-up: once three months of usage data exists, replace the B3 "delta" column with a data-driven score derived from the gap between an IC's current output and their observed demand on the tool. Until then the manager scores B3 by informed judgement. -->
