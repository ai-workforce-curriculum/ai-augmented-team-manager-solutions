# Exercise 04 — The AI-ROI Story for the Exec Pre-Read

> Module: `mod-104-productivity-measurement-and-ai-roi`
> Exercise type: **design** (worked pre-read + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is a **one-to-two-page exec pre-read** that tells the ROI story for a team's AI tool rollout — not a dashboard, not a victory lap, and not a budget request. It is the artefact an exec reads *before* a meeting so that the meeting can be about decisions rather than definitions.

A good pre-read is:

- **Decision-first.** It opens with the decision being asked of the reader (continue, expand, cut, re-scope). Every paragraph after serves that decision.
- **Numerate but numeracy-honest.** Numbers are paired with the study design that produced them, the confounders that could explain them, and the counter-metrics they do and do not move.
- **Short.** One page if you can; two if you must. Execs skim; the skim must still convey the decision and the headline.
- **Written to survive hostile reading.** A skeptical CFO or director will ask the uncomfortable questions in the margins; the pre-read should answer them before they are asked.

Design decisions this solution encodes:

1. **ROI is a ratio, not a number.** You cannot report a return without the cost it is returned against. The pre-read's structure is cost line → value claim → counter-metric → confidence.
2. **Confidence is a first-class field.** Every claim has a level — observational / correlational / causal — and the pre-read states which. This is the single highest-leverage habit.
3. **Counter-metrics are presented, not hidden.** If throughput rose and change failure rate rose, say so. The exec will find out; better they find out from you.
4. **The pre-read does not oversell "productivity %" numbers.** Benchmark figures from external studies are not internal results; the pre-read never implies they are.
5. **There is a named decision owner and a named escalation path.** No pre-read without an owner.
6. **The pre-read is written to be *re-read* a quarter later.** It records what we believed, so that next quarter we can check whether we were right. This is how institutional memory about AI rollout actually forms.

## 2. Implementation — the pre-read template, with a filled example

### 2.1 Template (drop into `pre-reads/<yyyy-qq>-ai-roi.md`)

```markdown
# AI Tool Rollout — ROI Pre-Read, <team name>, <quarter>

> Decision being asked: **<continue / expand / re-scope / cut>**
> Decision owner: **<named human>** · Prepared by: **<team manager>** · Date: **<yyyy-mm-dd>**
> Read time: ~4 minutes.

## TL;DR
<Two sentences. State what is being proposed, state the recommendation, state
the single number that most justifies it, state the single risk that could
reverse it.>

## 1. What we rolled out
- **Tool:** <vendor + feature, e.g., "Claude Code CLI, agent mode">
- **Cohort:** <who has access, how many engineers, since when>
- **Scope:** <what workflows it touches — PR drafting, review, incident
  triage, docs — and what it is explicitly not used for>

## 2. What it cost (last full quarter)
- Fixed seats: $<X>
- Variable usage: $<Y> (p50 $<a> / eng-mo; p90 $<b> / eng-mo)
- Second-order (CI minutes, cloud compute): $<Z>
- **Total:** $<X+Y+Z>
- Reconciled with finance invoices within <%> variance. Source: Exercise 03 dashboard.

## 3. What moved
Each row names the metric, the delta, the study design, and the confidence.

| Metric | Baseline | Current | Delta | Design | Confidence |
|---|---|---|---|---|---|
| <Diffs / eng / week> | <n> | <n> | <+/- %> | Within-firm pre/post | Observational |
| <Change failure rate> | <n>% | <n>% | <+/- pp> | Within-firm pre/post | Observational |
| <DevEx score> | <n>/5 | <n>/5 | <+/- > | Quarterly survey, same instrument | Self-report |
| <Speed-of-impact> | <n> days | <n> days | <+/- %> | Within-firm pre/post | Observational |
| <Adoption (daily actives)> | 0 | <n>% | — | Telemetry | Factual |

## 4. What did NOT move (and why we think so)
- <Named metric that did not move>, because <leading hypothesis>.
- <Counter-metric that moved the wrong way, if any>, severity <n>, explanation.

## 5. Confounders we cannot rule out
- <Co-occurring change 1: team re-org / platform migration / hiring>
- <Co-occurring change 2>
- <Co-occurring change 3>
These are why the "Delta" column is attributed `Observational`, not `Causal`.

## 6. What we are asking for
- **The ask:** <1–3 sentences stating the decision and what it costs.>
- **What success looks like at the next check-in:** <specific numeric or
  qualitative criteria, tied to the Core 4 dashboard and the DevEx survey.>
- **What would make us reverse this:** <see rollback criteria — Exercise 06.>

## 7. Appendices (linked, not inline)
- Core 4 dashboard — [link]
- Cost dashboard (Exercise 03) — [link]
- Quarterly DevEx survey results — [link]
- Literature digest we are and are not citing from — [link] (Exercise 02)
- Rollback criteria & what-we-got-wrong drill — [link] (Exercise 06)
```

### 2.2 Worked example — one filled version

The following example uses **illustrative numbers** for a hypothetical 10-engineer platform team. Nothing in it is a real incident or published metric; it is a worked example of shape.

```markdown
# AI Tool Rollout — ROI Pre-Read, Platform Team, 2026-Q3

> Decision being asked: **continue, no expansion this quarter**
> Decision owner: Director of Engineering (<name>)
> Prepared by: Team Manager (<name>) · Date: 2026-10-07
> Read time: ~4 minutes.

## TL;DR
We continued the Claude Code CLI rollout to the full platform team this
quarter; recommend we **keep the current scope and defer expansion to
adjacent teams until we have two more weeks of CFR data**. The single
number most justifying this: our p50 Speed-of-impact dropped from 7.2 to
5.1 days with no measurable increase in CFR. The single risk that could
reverse it: a 2-week CFR uptick in late September that we do not yet
have a clean explanation for.

## 1. What we rolled out
- **Tool:** Claude Code CLI, agent mode, plus hosted review workflow.
- **Cohort:** 10 platform engineers; rolled out in week 1 of Q2.
- **Scope:** PR drafting, PR review assistance, docs. Explicitly NOT used
  for incident on-call commands, auth/authz code generation, or any
  prompt touching customer PII.

## 2. What it cost (last full quarter)
- Fixed seats: $4,200
- Variable usage: $6,750 (p50 $55 / eng-mo; p90 $180 / eng-mo)
- Second-order (CI minutes, cloud compute): $1,100
- **Total:** $12,050. Reconciled with finance within 1.4%. Source: Exercise 03
  dashboard.

## 3. What moved
| Metric | Baseline (Q1) | Current (Q3) | Delta | Design | Confidence |
|---|---|---|---|---|---|
| Diffs / eng / week | 3.1 | 4.0 | +29% | Within-firm pre/post | Observational |
| Change failure rate | 8.4% | 8.9% | +0.5 pp | Within-firm pre/post | Observational |
| DevEx score | 3.6/5 | 4.1/5 | +0.5 | Quarterly survey | Self-report |
| Speed-of-impact (p50) | 7.2 days | 5.1 days | -29% | Within-firm pre/post | Observational |
| Adoption (daily actives) | 0% | 86% | — | Telemetry | Factual |

## 4. What did NOT move (and why we think so)
- Deploy frequency (unchanged at ~5/day). The deploy cadence is gated by
  release-train policy, not throughput; we did not expect this to move.
- **Late-September CFR uptick (2 weeks at ~11%).** We do not yet have a
  clean explanation. It overlaps with our platform-DB migration, so it is
  not cleanly attributable to the tool.

## 5. Confounders we cannot rule out
- Three new senior hires joined mid-Q2; onboarding productivity curves
  may explain part of the Diffs/eng movement.
- Platform-DB migration completed in week 4 of Q2; CI flakiness and CFR
  are both sensitive to migration timing.
- The quarterly DevEx survey was fielded two weeks after a well-liked
  company-wide policy change; the +0.5 includes an unknown amount of
  mood lift that is not attributable to the tool.

These are why section 3's deltas are `Observational`, not `Causal`.

## 6. What we are asking for
- **The ask:** Approve continued spend at current scope (~$12k/quarter);
  defer expansion to adjacent teams by one quarter so we can observe CFR
  stabilise post-migration.
- **What success looks like at the next check-in:** Speed-of-impact
  holds at or below 5.5 days p50; CFR returns to or below the 8–9% band
  for 6 consecutive weeks; DevEx score holds ≥ 4.0.
- **What would make us reverse this:** See Exercise 06 rollback criteria.
  Short version: sustained CFR > 11% for 4 consecutive weeks, or a
  Sev-1 incident with a clean causal line back to AI-generated code.

## 7. Appendices
- Core 4 dashboard — <link>
- Cost dashboard — <link>
- Q3 DevEx survey — <link>
- Literature digest — <link>
- Rollback criteria — <link>
```

### 2.3 Why the pre-read is shaped this way

- **TL;DR first** because execs read top-to-bottom and stop when they have a decision. If the TL;DR is inadequate, the rest does not save it.
- **Section 2 (cost) precedes section 3 (what moved)** because the reader cannot interpret productivity gains without the cost they cost. Reversing the order invites the "but how much did this *cost*?" interrupt.
- **Section 3's confidence column is non-negotiable.** Reporting a delta without a confidence label is how teams get into trouble a quarter later when the number fails to reproduce.
- **Section 4 ("what did NOT move") is a credibility move.** A pre-read that only reports wins reads as marketing. A pre-read that reports both wins and nulls gets read.
- **Section 5 (confounders) protects against over-claiming.** Co-occurring changes (hires, migrations, policy shifts) are the modal cause of "AI-attributed" gains. Name them.
- **Section 6 (the ask)** is separated from the data so an exec can land on the decision without re-reading the numbers.
- **Section 7 (appendices) is linked, not inline.** The pre-read's job is to be read; the appendices' job is to be opened only if trust is challenged.

## 3. Validation steps

1. **Red-team read.** Hand the draft pre-read to one skeptical peer (another team manager or a finance partner). Ask them to redline any sentence that reads as over-claiming. Fix every red line before the pre-read ships.
2. **One-page test.** Print it. If section 6 (the ask) does not appear on page 1, the pre-read is too long. Cut from section 3's secondary metrics first; they belong in the appendix.
3. **Four-minute timer.** Read the pre-read aloud. If you cannot finish in four minutes, it is too long for an exec pre-read; it is a quarterly review. Cut.
4. **Confounder-naming count.** Count the named confounders in section 5. If fewer than three, re-work — zero or one confounder named is a signal of over-claiming.
5. **Counter-metric present?** Confirm that section 3 contains at least one metric that moved the "wrong" way or did not move. A pre-read with no counter-metric is not a pre-read; it is a vendor deck.
6. **Confidence consistency.** For each delta in section 3, verify the "design" and "confidence" columns match the data source. An observational delta labelled "causal" is the single most dangerous row.
7. **Re-read test (one quarter later).** Keep every pre-read; at the start of the next quarter, open last quarter's and check: were the deltas we predicted the ones we got? Were the confounders real? This is the exercise that compounds over years into a team that measures AI ROI well.

Validation is **adversarial**: assume a skeptical reader will land on the weakest sentence and question your credibility on the whole document.

## 4. Rubric

Use this to grade a submitted exec pre-read. Each criterion scores 0–3.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Decision framing** | No explicit ask | Ask implied | Explicit ask + decision owner | Explicit ask + decision owner + reversal criterion |
| R2 | **Cost-side completeness** | Fixed seats only | Fixed + variable | Fixed + variable + second-order + finance reconciliation % | All of 2 + per-engineer p50/p90 breakdown |
| R3 | **Metric movement with design** | Deltas only | Deltas + metric names | Deltas + metric names + design column | All of 2 + confidence column |
| R4 | **Counter-metric / null handling** | Absent | Null acknowledged in prose | Dedicated section for "what did not move" | "Did not move" section + at least one metric that moved the wrong way reported honestly |
| R5 | **Confounder naming** | Absent | 1 confounder | 3+ confounders | 3+ confounders + explicit link from confounders to the `Observational`-not-`Causal` labelling in section 3 |
| R6 | **Literature discipline** | Vendor figures cited as internal results | Mixed, unclear | Only internal results in section 3; vendor/literature figures in a bounded appendix | Explicit statement that external study figures are not internal results, with pointer to Exercise 02 |
| R7 | **Length and skim-ability** | > 2 pages, dense | 2 pages, no TL;DR | 1–2 pages + TL;DR | 1 page + TL;DR + section 6 fits on page 1 |
| R8 | **Institutional-memory hook** | None | Pre-read is a one-off | Pre-read is dated and archived | Dated + archived + explicit re-read cadence (quarterly) + success/reversal criteria the next pre-read can grade itself against |

**Pass bar: 16/24.** Below that, iterate before sending to execs.

## 5. Common mistakes

- **Opening with the number.** The TL;DR is not the number — it is the decision. If the first sentence is "productivity increased 29%," the pre-read is marketing. The first sentence is "we recommend X."
- **Omitting the cost.** ROI without a denominator is not ROI; it is Return. Section 2 is non-negotiable.
- **Reporting only wins.** The most expensive thing a team manager can do politically is leave a counter-metric for the exec to discover. Name it.
- **Citing external benchmark figures as if they were internal results.** "Developers are 55% faster with the assistant" (Peng et al. 2023) is a *single-task external RCT* result, not a claim your team can make about its own throughput. Keep internal and external strictly separated. Exercise 02 is the fuse.
- **Labelling observational deltas "causal."** Correlation across a within-firm pre/post with co-occurring changes is observational at best. Use the language that matches.
- **Burying the ask.** An exec who reads two pages and still does not know what you are asking for will leave the decision for next quarter. Section 6 must be unmissable.
- **No reversal criterion.** A pre-read that cannot specify what would make it reverse its recommendation is a pre-read that is pretending the risk is zero. Point to Exercise 06.
- **Writing a new pre-read from scratch every quarter.** The point of the template is comparability across quarters. Deviate on content, not on shape.
- **Appendices inline.** The pre-read becomes unreadable. Link.
- **No author and no date.** A pre-read without an author is a pre-read without accountability. Both go in the header.

## 6. References

- Forsgren, N., Storey, M.-A., Maddila, C., Zimmermann, T., Houck, B., & Noda, J. (2021). *The SPACE of Developer Productivity.* ACM Queue, 19(1). <https://queue.acm.org/detail.cfm?id=3454124> — the "never a single metric" discipline is the backbone of section 3.
- Tacho, L. & Noda, A. (2024). *DX Core 4.* DX / `getdx.com`. <https://getdx.com/research/measuring-developer-productivity-with-the-dx-core-4/> — supplies the default metrics in the example's section 3.
- **DORA State of DevOps** (annual) at <https://dora.dev/> — the Change Failure Rate heritage and the AI-adoption cross-tabs are referenced when interpreting what did and did not move.
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the Govern function's requirement for documented accountability and the Measure function's requirement to track AI system performance against defined metrics map directly onto sections 5 and 6.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) and [MITRE ATLAS](https://atlas.mitre.org/) — relevant when the "what would make us reverse this" criterion in section 6 is a security incident, not a productivity regression; see also Exercise 06.
- Exercise 01 (DX Core 4 / SPACE comparison), Exercise 02 (literature digest), Exercise 03 (cost tracking), Exercise 06 (rollback criteria) — the pre-read composes these.

<!-- follow-up: after two quarters, add a "What we got wrong last quarter" paragraph at the top of each pre-read referencing the previous quarter's success/reversal criteria. This is the single highest-value section once the cadence is running. -->
