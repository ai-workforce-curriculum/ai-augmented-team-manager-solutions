# Exercise 04 — The Commissioning Rubric for Eval Work

> Module: `mod-103-review-standards-and-eval-commissioning`
> Exercise type: **design** (worked rubric + decision rationale + grading rubric)

## 1. Solution overview

Teams commission eval work ("build me a quality gate for our prompts" / "evaluate our new model before rollout") the same way they commission any other engineering work — with a ticket, a spec, and acceptance criteria. The special problem with eval work is that it's easy to produce a deliverable that *looks like* an evaluation (a notebook, a few numbers, a Slack post) without being one that a manager can act on.

The deliverable here is a **commissioning rubric**: a short template a team manager fills out *before* the work starts, and uses *during* review to decide whether the delivered eval is complete. It makes four things explicit that are usually fuzzy:

1. **What decision will this eval inform?** (Rollout / rollback / prompt change / model swap.)
2. **What is the acceptable failure rate and on which inputs?**
3. **Who runs the eval, how often, and who sees the result?**
4. **What constitutes passing?**

Design decisions this solution encodes:

1. **Commissioning is upstream of implementation.** A rubric completed *after* the eval is just a post-hoc defense. The rubric's value is forcing clarity before someone writes code.
2. **"Decision-first" framing.** Every eval exists to inform a decision. If the commissioning rubric cannot answer "what decision will this unblock?" the eval shouldn't be built yet.
3. **Three measurable layers, in increasing rigor.** Smoke evals (does it run?), capability evals (does it meet a bar?), safety evals (does it fail safely?). Not every change needs all three, but the rubric forces an answer.
4. **Dataset provenance is non-negotiable.** Where did the inputs come from? Is PII scrubbed? Can the dataset be regenerated? An eval without dataset provenance is unauditable.
5. **The rubric is scored, not just filled.** A scored rubric yields a go/no-go number; a rubric-as-form is easy to half-fill.

## 2. Implementation — the rubric

### 2.1 Commissioning rubric template

```markdown
# Eval Commissioning Rubric — <feature / change>

> Fill this out **before** the eval is built. Attach to the ticket.
> Review it on delivery: if any row drops below its minimum, the eval is
> not accepted yet.

## A. Framing

- **A1. Decision this eval informs.** (One sentence: "This eval decides
  whether we ship <model/prompt change> to <audience> on <date>.")
- **A2. Change being evaluated.** (e.g. prompt v1.3 → v1.4; model
  sonnet-4-6 → opus-4-7.)
- **A3. Decision owner.** (Named human, not a team.)
- **A4. Rollback plan if the eval fails after ship.** (Two sentences max.)

## B. Scope of the eval

- **B1. Behaviour being measured.** (Reading-level fit, refusal
  appropriateness, factual accuracy against a KB, PII leakage, latency,
  cost, …)
- **B2. Not being measured.** (Explicit non-goals — what this eval is
  *silent* on. Important so stakeholders don't over-read results.)
- **B3. Risk tier.** (Low / Medium / High per the team's risk policy.)

## C. Dataset

- **C1. Provenance.** Where the inputs came from — synthetic, production
  traffic (sampled how?), human-authored, borrowed from a benchmark.
- **C2. Size.** Counts by category. If you can't justify N, you probably
  need more; see rubric guidance below.
- **C3. PII / sensitive data handling.** Scrubbed? By whom? With what
  tool? Retention policy for the dataset?
- **C4. Reproducibility.** Can the dataset be regenerated, and does the
  spec allow swapping in refreshed inputs without changing thresholds?
- **C5. Hold-out vs. dev split.** Is there a hold-out set that is **not**
  looked at during prompt / model tuning?

## D. Metrics

- **D1. Primary metric.** The one number the decision owner looks at.
  State its range, direction (higher-is-better / lower-is-better), and
  threshold.
- **D2. Secondary metrics.** Up to three. Each with threshold or "report
  only, no gate".
- **D3. Statistical treatment.** Mean / median / p90 / pass-rate /
  Wilson confidence interval / bootstrap? Which one and why.
- **D4. Sensitivity to prompt wording.** If the metric is an LLM-as-judge
  score, state the prompt and the judge's calibration (see Exercise 05).

## E. Who passes and who fails

- **E1. Pass threshold.** The specific condition under which the change
  ships ("primary metric ≥ 0.92 on hold-out; no PII leak in 500 inputs").
- **E2. Fail threshold.** The specific condition under which the change
  does **not** ship. Must be distinct from "not pass" — gives a clear
  rollback trigger.
- **E3. Judgement zone.** What lies between pass and fail, and who
  decides.

## F. Ops

- **F1. Where it runs.** (CI, a cron, a human-run notebook.)
- **F2. How often.** (On every PR to `prompts/`? Nightly? Pre-release?)
- **F3. Where results land.** (Dashboard URL, Slack channel, GitHub
  check.)
- **F4. Who gets paged / notified on failure.** (Names, not teams.)
- **F5. Expected total compute cost per run.** (Dollars, minutes.)

## G. Deliverables checklist (filled on acceptance)

- [ ] Dataset file(s) committed or referenced with hash.
- [ ] Eval configuration file committed.
- [ ] One documented run attached (output, timestamp, judge calibration).
- [ ] Threshold values live in config, not hard-coded in script.
- [ ] README explains how to re-run locally in under 10 minutes.
- [ ] Rollback plan from A4 linked to a runbook.
```

### 2.2 Worked example — commissioning the reading-level eval

(This is the same evaluation driven in Exercise 05.)

```markdown
# Eval Commissioning Rubric — reading-level prompt v2 for team weekly summaries

## A. Framing
- **A1. Decision this eval informs.** This eval decides whether to ship
  `summary-prompt.v2` to the team-manager-copilot internal beta on the
  next Monday deploy.
- **A2. Change being evaluated.** `summary-prompt.v1.3` → `v2`.
- **A3. Decision owner.** Priya Rao (eng lead).
- **A4. Rollback plan if the eval fails after ship.** Revert
  `summary-prompt` to the previous commit via the config-flag override
  in LaunchDarkly; same-hour revert; no data migration.

## B. Scope of the eval
- **B1. Behaviour being measured.** Average reading grade level of the
  summary stays in the band 8.0–10.5 (Flesch-Kincaid), and factual
  claims in the summary are present in the source transcript.
- **B2. Not being measured.** Latency, cost, tone, humour, tool-use
  behaviour. Those are tracked on separate dashboards.
- **B3. Risk tier.** Medium (internal-only beta, no customer exposure).

## C. Dataset
- **C1. Provenance.** 120 real weekly standup transcripts from Q3,
  consented teams only, with team names replaced by placeholders via
  `scripts/scrub.py`. 20 synthetic edge-case transcripts (meeting cut
  off, single speaker, high-jargon) hand-authored by the eng lead.
- **C2. Size.** 140 total; 100 dev / 40 hold-out; roughly balanced across
  team sizes 3/5/8/12.
- **C3. PII / sensitive data handling.** Names, email addresses,
  customer identifiers scrubbed. Dataset lives under the same retention
  policy as prod logs (90 days).
- **C4. Reproducibility.** Regen script `scripts/build_summary_dataset.py`;
  dataset hash committed; re-running against fresh transcripts does not
  require tweaking thresholds.
- **C5. Hold-out vs. dev split.** 40-transcript hold-out never looked at
  during prompt tuning. Pass threshold measured on the hold-out only.

## D. Metrics
- **D1. Primary metric.** Pass rate on hold-out, where "pass" means
  reading grade ∈ [8.0, 10.5] AND factual-support judge rating ≥ 4/5.
  Threshold: pass rate ≥ 0.90.
- **D2. Secondary metrics.**
  - Mean reading grade (report only).
  - Median token count (threshold: ≤ 450).
  - PII-leak-check pass rate (threshold: 1.00; any leak fails the eval).
- **D3. Statistical treatment.** Pass rate reported with Wilson 95% CI
  on N=40. Threshold compared against the lower bound of the CI.
- **D4. Sensitivity to prompt wording.** Judge prompt committed; judge
  calibrated against 20 human-rated examples (κ > 0.6).

## E. Who passes and who fails
- **E1. Pass threshold.** Primary ≥ 0.90 (Wilson lower bound) AND PII
  pass rate = 1.00.
- **E2. Fail threshold.** Primary < 0.80 OR any PII leak.
- **E3. Judgement zone.** 0.80 ≤ primary < 0.90: eng lead convenes a
  decision meeting with the design lead; not auto-shipped.

## F. Ops
- **F1. Where it runs.** CI job `evals-summary`, Promptfoo runner.
- **F2. How often.** On every PR touching `src/team_manager/prompts/`
  AND nightly.
- **F3. Where results land.** GitHub check + Slack
  `#team-copilot-eval` daily post.
- **F4. Who gets paged / notified on failure.** `#team-copilot-eval`
  channel; eng lead if two consecutive nightly runs fail.
- **F5. Expected total compute cost per run.** ~$3 per run at current
  model prices; ~$90 / month at nightly + ~12 PRs / month.

## G. Deliverables checklist
- [x] Dataset file(s) committed under `tests/evals/summary/dataset/`.
- [x] Eval configuration `tests/evals/summary/promptfooconfig.yaml`.
- [x] One documented run `tests/evals/summary/runs/2026-11-10.json`.
- [x] Thresholds live in `tests/evals/summary/thresholds.yaml`.
- [x] README `tests/evals/summary/README.md` with 10-minute local recipe.
- [x] Rollback plan links to `docs/runbooks/rollback-prompt.md`.
```

### 2.3 Decision rationale (why this shape)

- **Commissioning rubric, not an eval design doc.** A design doc is prose; a commissioning rubric is **boxes to fill.** That difference is why it gets filled.
- **Row A first.** The most common failure mode is that the eval is built before anyone decided what question it answers. If A is blank, stop; nothing else is worth writing.
- **C — dataset provenance — before D — metrics.** A good metric on a bad dataset is worse than no eval, because it produces false confidence. Order forces the author to pick the inputs before picking the ruler.
- **Hold-out as a named row.** Teams routinely hyper-tune on the same dataset they report numbers from. Naming the hold-out and tracking who has touched it is the discipline that prevents overfit prompts shipping to prod.
- **E separates pass and fail with a *judgement zone*.** Pass/fail binaries fail silently when numbers land near the threshold. A judgement zone captures the "convene a meeting" range explicitly.
- **F forces ops engagement.** An eval that runs nowhere is theatre. Naming channel, cadence, and compute cost reveals whether the organisation can actually operate the thing.
- **G is a checklist, not prose.** On delivery, boxes-and-ticks is faster to review than reading a report.

The rubric maps to governance frameworks:

- **NIST AI RMF, Measure function.** The rubric operationalises **Measure 2.1 (test, evaluation, verification), 2.7 (human-AI interaction), 2.11 (fairness and bias)** by forcing a *decision-connected* measurement spec.
- **NIST AI RMF, Manage function.** Rows A4 and F4 provide the rollback and notification elements required by **Manage 4.1 (post-deployment monitoring)**.

## 3. Validation steps

1. **Tabletop a past change.** Pick an eval the team has already shipped (or an evaluation you wish had been commissioned). Retrofit the rubric. Where it is uncomfortable to fill is where the eval was underspecified.
2. **Fill it on a current ticket.** Walk through each row with the engineer who will build it. Rows A, D, and E tend to produce the most clarifying discussion.
3. **Blind-review check.** Give the completed rubric to a peer who was not in the commissioning conversation. If they can state the decision, the pass/fail criteria, and the rollback plan back to you, the rubric is doing its job.
4. **Delivery review.** On eval acceptance, go row by row. Any row that cannot be verified against the delivered artefact is either an incomplete deliverable or a rubric row that was never actionable; redline both.
5. **Cost sanity check.** Multiply F5 by F2's cadence. If projected eval compute is > 10% of production compute cost, revisit — your eval dataset is too big or your cadence is too eager.
6. **Keep a log.** After 5–10 commissioned evals, review which rubric rows most often produce changes to the commission vs. which are always filled perfunctorily. Reshape the rubric accordingly.

## 4. Rubric for grading a submitted commissioning template

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Decision-first framing** | No row asks what decision the eval informs | Row exists but vague | Decision named, owner named | Decision named, owner named, rollback plan named |
| R2 | **Scope + non-goals** | Not covered | Scope only | Scope + non-goals | Scope + non-goals + risk-tier field |
| R3 | **Dataset provenance** | Not asked | "Where from" question only | Provenance + PII + size | Provenance + PII + size + regen script + hold-out |
| R4 | **Primary vs. secondary metrics** | One metric dump | Metrics + direction | Primary + secondaries + thresholds | Primary + secondaries + thresholds + statistical treatment |
| R5 | **Pass / fail / judgement zone** | Pass only | Pass + fail | Pass + fail distinct | Pass + fail + judgement-zone owner |
| R6 | **Ops rows** | None | Where + cadence | Where + cadence + alerts | Where + cadence + alerts + named recipients + compute cost |
| R7 | **Deliverables checklist** | None | Prose list | Checkable list | Checkable list that maps to specific files / configs |
| R8 | **Shape forces upstream work** | Form could be filled retrospectively without affecting the eval | Partially | Several rows demand upstream clarification | Rows A, C, E unambiguously block implementation until filled |
| R9 | **Worked example** | None | Toy | Real-looking, partial | Real-looking, complete, passes the rubric itself |

**Pass bar: 20/27.**

## 5. Common mistakes

- **Starting with metrics.** "We'll measure accuracy and latency" without asking what decision that informs. The result is numbers, not answers.
- **No decision owner.** "The team" is not a decision owner. A name is.
- **Pass threshold = "better than before".** Better on which dataset? By how much? Does the improvement exceed the confidence interval? A threshold with a confidence interval is a threshold; without one, it's a vibe.
- **Dev-set numbers reported as results.** Shipping a prompt because it hit 0.95 on the dev set it was tuned on is a textbook overfit; the rubric's hold-out row exists to make this hard.
- **PII in test fixtures.** Real transcripts get checked in because the "scrubbed" directory wasn't used. The rubric's row C3 catches this.
- **"We'll run it manually when we remember."** If cadence is manual, the eval doesn't exist. Make F2 concrete or de-scope.
- **Compute cost unspoken.** Eval compute is a line item. Teams discover six months in that nightly evals cost more than the prod service. Row F5 forces the conversation up front.
- **Auto-ship on judgement-zone.** The whole point of the judgement zone is that automation hands off to humans; wiring it to auto-ship defeats the purpose.
- **Ignoring secondary signals.** A prompt change that improves primary but doubles token cost is often a net loss. Secondary metrics exist to catch this.
- **Rubric as a form, not a scored artefact.** Teams fill the boxes and forget. The grading step (Section 4) is what keeps rows from degenerating into placeholders.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the **Measure** function (especially sub-categories 2.1, 2.7, 2.11) and the **Manage** function (4.1) directly motivate the rubric's framing / dataset / metrics / ops rows.
- [OWASP Top 10 for Large Language Model Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — LLM06 (Sensitive Information Disclosure) motivates the PII row and the PII-pass-rate secondary metric.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — ML04 (Membership Inference) and ML05 (Model Theft) informed the hold-out / reproducibility requirements.
- [MITRE ATLAS](https://atlas.mitre.org/) — used to validate that the rubric's PII-leak secondary metric covers the categories of data exfiltration relevant to LLM deployments.
- [Promptfoo documentation](https://www.promptfoo.dev/docs/) — supplies the concrete runner the worked example assumes; threshold-gate pattern used here is standard Promptfoo configuration (see Exercise 05 for the running config).

<!-- follow-up: once the team has commissioned 10+ evals under this rubric, replace the illustrative compute-cost line in F5 guidance with a per-category median derived from the team's own runs. -->
