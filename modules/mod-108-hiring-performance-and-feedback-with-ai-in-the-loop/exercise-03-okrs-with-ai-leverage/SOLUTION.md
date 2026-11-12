# Exercise 03 — OKRs (or KPIs) With AI Leverage

> Module: `mod-108-hiring-performance-and-feedback-with-ai-in-the-loop`
> Exercise type: **design** (worked quarterly OKR set + authoring heuristics + grading rubric)

## 1. Solution overview

The deliverable is a **worked quarterly OKR set** (three objectives, two to
three key results each) for a platform-API team that has adopted AI
assistants heavily, with every key result paired to an explicit
counter-metric and an *AI-leverage adjustment note* that explains how the
baseline moved once assistants landed and whether the target is sandbagged,
flat, or stretched. It is the manager's first draft that goes into
calibration with their skip and peer managers, together with a short
authoring-heuristics block and a rubric that a peer manager can use to grade
their own draft.

A good OKR set under heavy AI augmentation is:

- **Outcome-weighted, not output-weighted.** PRs merged, lines of code,
  issues closed, and "assistant usage" are not key results. Latency, change
  failure rate, partner activation, and incident learnings are. See
  mod-104 Exercise 02 for the full argument against output accounting under
  AI leverage; this exercise is where that argument becomes a planning
  artifact.
- **Paired.** Every throughput or velocity KR has a quality, reliability,
  or review-catch counter-metric on the same row. A throughput KR without a
  counter-metric is below the rubric bar — assistants amplify volume
  cheaply and will game a one-sided target.
- **Honest about leverage.** Each KR says what the baseline looked like
  before assistants were in the loop, what shifted after adoption, and
  whether the proposed target is *sandbagged* (set below demonstrated
  post-adoption capacity), *flat* (held at pre-adoption level because
  quality counter-metric constrains it), or *stretched* (above
  demonstrated capacity on purpose).
- **Tool-agnostic.** KRs describe behaviours and outcomes. They do not name
  specific assistants, vendors, or SKUs — those rotate quarter to quarter,
  and an OKR tied to a product rewrite becomes meaningless when the
  product is retired.
- **Anti-atrophy by construction.** One KR per quarter must be about team
  *learning* and *review-catch of AI failure modes*. This is the
  counterweight to the volume-amplifying effect of the other KRs and the
  bridge to the mod-107 anti-atrophy work.
- **Explicit about what is NOT on the list.** An anti-scope section names
  the metrics the team deliberately did not pick up, so that calibration
  catches drift rather than silent re-prioritisation.

Design decisions this solution encodes:

1. **Targets are set against the post-adoption baseline, not the
   pre-adoption one.** Setting a stretch target by taking last year's
   number and adding 20% is the default failure mode for AI-leveraged
   teams. The cheap gain already happened; the target must reflect that or
   it is sandbagged by default.
2. **Counter-metrics are not aspirational — they are gates.** If the
   counter-metric regresses past its gate, the paired throughput KR is
   scored zero regardless of attainment. This is the NIST AI RMF **Measure
   and Manage** functions encoded directly in the grading rule.
3. **Grading cadence and nomenclature are stated inside the OKR set.** The
   set names what "committed" and "stretched" mean for this team this
   quarter, and the dates of mid-quarter and end-quarter grading. Peer
   managers can then compare apples to apples at calibration.
4. **Headcount asks and OKRs are kept consistent but separate.** The
   headcount justification pre-read (sibling Exercise 06) consumes the
   output of this OKR set but is written as a different artifact. An OKR
   set that silently encodes a headcount argument is below the rubric bar.
5. **Security, governance, and prompt-injection posture appear as a KR,
   not an afterthought.** OWASP ML Top 10 and MITRE ATLAS categories that
   apply to a platform-API team under AI augmentation (injection,
   supply-chain poisoning of context, model output trust) are first-class
   outcomes, not a checklist on the side.
6. **The set is a draft to calibrate, not a decree.** It names its
   reviewers, the venue, and the review date, and it lists the questions
   the manager expects to be challenged on.

## 2. Implementation — the quarterly OKR set

### 2.1 Scope and prerequisites

- The team is a fictional **platform-API team of eight engineers** (six
  ICs, a tech lead, and the manager) that owns an internal REST/gRPC
  surface consumed by ~15 sibling product teams. AI assistants have been
  in heavy daily use for roughly three quarters, so a post-adoption
  baseline exists.
- The OKR set is published as a one-page markdown artifact with the
  grading rubric attached, plus an appendix pointing to the mod-104
  dashboards that feed each "how measured" column.
- Grading happens twice: a mid-quarter review at week 6 (course-correct
  only, no scoring) and an end-quarter grading at week 13. Both are
  recorded.
- The OKR set references but does not restate the team's incident-response
  runbook, review standards (mod-103), or the productivity dashboards
  (mod-104).

### 2.2 Worked OKR set

```markdown
# Platform-API team — Q-next OKRs, v0.1
Owner: manager. Reviewers: skip, two peer managers, tech lead.
Grading cadence: mid-quarter (week 6, no score) + end-quarter (week 13, scored).

## Grading nomenclature (team-local)
- "Committed" KR: team has line-of-sight to 1.0 given current staffing and
  known work. Missing a committed KR is a serious signal.
- "Stretched" KR: 0.7 is a strong result. 1.0 requires unanticipated
  leverage. Missing a stretched KR is expected ~30% of the time.
- Counter-metric gate: if the counter-metric regresses past its stated
  threshold at grading time, the paired throughput KR scores 0 regardless
  of attainment. Documented explicitly so no one is surprised.

## Objective 1 — Platform API is boringly reliable for its consumers
Why: consumer teams rely on us to be invisible. AI-assisted change
velocity on our side has risen; their perception of stability must not
fall.

| KR | Target | Baseline | Counter-metric (gate) | How measured | AI-leverage adjustment note |
|----|--------|----------|------------------------|--------------|------------------------------|
| 1.1 (committed) p99 read latency ≤ 180 ms across top-10 endpoints for 12 of 13 weeks | ≤ 180 ms weekly | p99 ran 170-220 ms last 2 quarters post-adoption | Change failure rate ≤ 15% (DORA definition); gate: > 20% zeroes the KR | Weekly SLO dashboard; CFR from deploy tracker + incident log | Pre-adoption p99 was 160-200 ms with fewer deploys/wk. Post-adoption deploy count roughly doubled; latency drifted up. Target is **flat** against pre-adoption, **stretched** against post-adoption drift. |
| 1.2 (committed) Partner-visible incidents (Sev-1/Sev-2 that reach a consumer team) ≤ 3 for the quarter | ≤ 3 | 5 last quarter, 4 the one before | Mean time to detect ≤ 10 min; gate: MTTD > 20 min zeroes the KR | Incident tracker, cross-checked against consumer-team pages | Post-adoption, incident *count* held flat but assistant-authored changes appeared in 2 of last quarter's 5 RCAs. Target is **stretched** and depends on KR 3.1 landing. |
| 1.3 (stretched) Onboard 2 new consumer teams to the v2 API with zero regression tickets in their first 30 days | 2 teams, 0 regressions | 1 team last quarter, 2 regressions | Consumer-team CSAT on integration survey ≥ 4/5; gate: < 3.5/5 zeroes the KR | Rollout tracker, consumer survey at day 30 | Assistant-drafted client SDK snippets shortened our integration-support time per team. Target assumes that holds; if it does not, this KR falls to 1 team. **Stretched** against post-adoption baseline. |

## Objective 2 — API surface earns its complexity
Why: assistants make it cheap to add endpoints. That is exactly the
failure mode we must police. Growth of the surface must be justified by
consumer value, not by ease of generation.

| KR | Target | Baseline | Counter-metric (gate) | How measured | AI-leverage adjustment note |
|----|--------|----------|------------------------|--------------|------------------------------|
| 2.1 (committed) Net new endpoints added ≤ 8, and each ≥ 2 consumer teams within 90 days of GA | ≤ 8 added, each ≥ 2 adopters | 14 added last quarter, median 1 adopter | Deprecations shipped ≥ 4; gate: < 2 deprecations zeroes the KR | API registry + adoption telemetry; deprecation tracker | Last quarter's endpoint count was inflated by assistant-drafted "just in case" surface. Target is **flat on value delivered** but a ~40% cut on raw surface growth. **Stretched** because deprecation work is unglamorous. |
| 2.2 (committed) Design-doc review turnaround median ≤ 3 business days with ≥ 1 named reviewer per doc who did a manual read-through | median ≤ 3 days, 100% have a human-attested reviewer | median 5 days, 70% had a named human reviewer | Reviewer-attested finding rate ≥ 0.4 findings/doc; gate: < 0.2 zeroes the KR | Doc tracker + reviewer attestation checkbox + review-comment counts | Assistants draft and review docs quickly; we need to confirm a human actually engaged. Target holds the review-catch rate against AI-drafted docs; see mod-103 Exercise 02 for the attestation pattern. **Stretched**. |

## Objective 3 — The team catches AI failure modes before customers do
Why: this is the anti-atrophy counterweight to Objectives 1 and 2. If
we only chase velocity, we lose the muscle that catches hallucinated
APIs, prompt-injected tool outputs, and poisoned context before they
reach production.

| KR | Target | Baseline | Counter-metric (gate) | How measured | AI-leverage adjustment note |
|----|--------|----------|------------------------|--------------|------------------------------|
| 3.1 (committed) ≥ 85% of merged PRs have a reviewer-attested check that AI-generated code was read line-by-line, and the quarterly "escaped AI defect" count is ≤ 2 | 85% attested + ≤ 2 escaped defects | New metric; 2 months of baseline data say current rate ~60% attested, 4 escaped defects last quarter | Review latency p75 ≤ 1 business day; gate: > 2 days zeroes the KR | PR template checkbox + post-incident tagging of escaped defects | Pre-adoption this KR was unnecessary. Post-adoption it is the single most important KR in the set. **Committed** because it is table stakes; see mod-107 Exercise 04 for the atrophy risk if it slips. |
| 3.2 (committed) Run 2 team-wide AI-failure-mode drills (prompt injection, context poisoning, hallucinated dependency) with written postmortems shared org-wide | 2 drills, 2 postmortems | 0 drills last quarter | Drill findings closed within 30 days ≥ 75%; gate: < 50% zeroes the KR | Drill calendar + postmortem repo + action-item tracker | Baseline is zero because this capability did not previously exist. Target is **committed** and modest; see MITRE ATLAS and OWASP ML Top 10 for scenario sourcing. |
| 3.3 (stretched) Every IC on the team has shipped 1 non-trivial change *without* assistant help this quarter and written a short reflection | 6/6 ICs | ~2/6 last quarter when informally tracked | Self-reported confidence delta ≥ +0.5 on a 5-point scale, measured pre/post; gate: aggregate confidence *drop* zeroes the KR | 1:1 log + reflection docs in team wiki | Anti-atrophy KR. Not about proving assistants are unnecessary — about preserving the unaided baseline. **Stretched**; links to mod-107 Exercise 02 practice schedule. |

## Anti-scope — what this OKR set deliberately does NOT target this quarter
- Assistant adoption rate, prompts/day, or any tool-usage metric.
- Raw PR count, LoC, or story-point velocity.
- Headcount growth (covered separately in Exercise 06 pre-read).
- Any KR naming a specific vendor product or SKU.
- "AI-generated code percentage" as a target — unmeasurable cleanly and
  incentivises the wrong behaviour.

## Open questions for calibration
- Is the 15% change-failure-rate ceiling consistent with sibling teams?
- Is the escaped-AI-defect definition tight enough that we can grade it?
- Should 3.3 be committed rather than stretched? Skip's call.
```

### 2.3 OKR authoring heuristics

Short checklist the manager uses while drafting:

- **Pair before you write.** For every throughput KR, write its
  counter-metric *first*, then negotiate the throughput target down to one
  the counter-metric can survive.
- **State the post-adoption baseline explicitly.** If the manager cannot
  state it, the target is a guess. Pull the baseline from the mod-104
  dashboards.
- **Ask: would an AI-amplified volume strategy score 1.0 on this KR
  without improving the thing we care about?** If yes, the KR is broken.
- **Prefer "consumer teams see X" to "we shipped X".** The consumer-side
  framing survives assistants writing more code.
- **Cap the number of KRs per objective at 3.** Under AI leverage,
  objective sprawl is the second failure mode after output accounting.
- **Keep one KR that costs velocity.** Objective 3 must have a KR that a
  pure-velocity actor would not pick. That is the signal the set is honest.
- **Date every artifact.** Baselines drift as assistants get better; a KR
  whose baseline is six months stale is sandbagged by default.

### 2.4 Why these objectives and KRs and not others

- Reliability (Obj 1) sits first because it is the externality AI-amplified
  change velocity threatens most directly. Scoring the API team on "ship
  more" and not "stay invisible" inverts the job.
- Surface-growth discipline (Obj 2) is on the set because endpoint
  proliferation is the specific AI failure mode for an API team. Without
  this objective, assistants will cheerfully help the team balloon the
  surface. See mod-106 Exercise 03 for the workflow-side counterpart.
- Failure-mode mastery (Obj 3) is on the set because none of the other
  objectives will survive long without it. The drills, the attestation
  rate, and the unaided-work KR together form the anti-atrophy spine.
- Alternatives rejected:
  - "Increase assistant adoption to 90%." Output metric, tool-specific,
    gameable, unrelated to outcomes.
  - "Reduce cycle time by 30%." No counter-metric, mod-104 Exercise 02
    already argues against this framing under AI leverage.
  - "Zero Sev-1 incidents." Not achievable; the discipline is in the
    MTTD/MTTR and consumer-visibility framing used in 1.2.
  - "All engineers level up one promo band." Compensation and ladder work
    (mod-107 Exercise 03) is not an OKR target; it is a planning input.
  - "Launch new product X." If the team has a launch, it sits inside an
    existing objective with its own counter-metrics; a launch is not an
    objective by itself because the OKR set outlives the launch.

## 3. Validation steps

Run these before taking the draft to calibration:

1. **Counter-metric coverage.** For every KR on a throughput or velocity
   dimension, read aloud the paired counter-metric and its gate. If any
   row has no counter-metric, fail validation.
2. **Gaming walk-through.** For each throughput KR, write two sentences
   describing how a motivated team with heavy assistant use could reach
   target without improving the outcome. If the counter-metric does not
   stop that strategy, tighten it.
3. **Baseline freshness.** Confirm every "baseline" number is derived from
   data no older than the last full quarter and that the mod-104
   dashboards can reproduce it on demand.
4. **Leverage-adjustment honesty check.** For each KR, state whether the
   target is sandbagged, flat, or stretched *out loud* to the tech lead.
   If the tech lead disagrees on more than two of the seven KRs, redraft.
5. **Anti-scope read-back.** Confirm the "deliberately does NOT target"
   list names at least four items and that each item is something a
   reasonable observer might have expected to see.
6. **Tool-agnosticism check.** Grep the draft for vendor names, product
   SKUs, and "percentage AI-generated". If any appear in a KR (not the
   notes), rewrite.
7. **Grading-rule check.** Confirm the committed/stretched definitions,
   the counter-metric gate semantics, and the grading dates are stated
   inside the set itself, not inferred.
8. **Anti-atrophy KR present and defensible.** Confirm Objective 3 (or
   equivalent) contains a KR that costs velocity. If a pure-velocity actor
   would pick every KR on this set, the set is unbalanced.

## 4. Rubric

Grade the OKR set out of 24. Pass bar: **17/24**, with no row at 0.

| # | Dimension | 0 | 1 | 2 | 3 |
|---|-----------|---|---|---|---|
| 1 | Outcome vs output framing | KRs are output counts (PRs, LoC, points) | Mix of output and outcome | Mostly outcome, one output slipped in | Every KR is outcome-framed; outputs appear only in "how measured" |
| 2 | Counter-metric coverage | Any throughput KR is unpaired | Counter-metrics exist but are soft (no gate) | Gates present on most paired KRs | Every throughput/velocity KR has a counter-metric with an explicit numerical gate |
| 3 | Leverage adjustment honesty | Baseline unstated or unchanged from pre-adoption | Baseline stated but target not labeled sandbagged/flat/stretched | Labels present on most KRs | Each KR carries explicit labels, and the labels are defended with data |
| 4 | Anti-atrophy KR | Missing | Vague ("invest in learning") | Named but weakly measured | Specific KR, measurable, costs velocity, linked to mod-107 anti-atrophy work |
| 5 | Tool-agnosticism | Vendor/SKU names inside KRs | Names in notes only | Names only in appendix | No vendor/product names appear anywhere in KR text |
| 6 | Grading cadence & nomenclature | Not stated | Dates given but no committed/stretched definition | Definitions given but no mid-quarter review | Committed/stretched defined in-team, mid- and end-quarter dates named, grading rule stated |
| 7 | Anti-scope section | Missing | One or two items | Several items, mostly obvious | ≥ 4 items, each a plausible "why not that?" a reviewer would ask |
| 8 | Governance / security presence | Absent | Mentioned in notes | A KR touches on it | A KR targets AI-failure-mode mastery with measurable actions tied to NIST AI RMF / OWASP ML Top 10 / MITRE ATLAS |

Interpretation: ≥ 20 is calibration-ready. 17–19 is a working draft that
needs one more pass. < 17, redraft before taking to skip.

## 5. Common mistakes

1. **Pre-adoption baselines reused as stretch targets.** The post-adoption
   baseline is the one the team must beat; using the older number
   produces a sandbagged target that looks ambitious.
2. **Throughput KRs with no counter-metric.** The most common failure;
   produces the exact AI-amplified volume strategy the exercise warns
   against.
3. **"Assistant adoption rate" or "prompts per IC per day" as a KR.**
   Tool-specific, gameable, output-flavoured, and hostile to the work the
   team is actually doing. Belongs in a vendor review, not an OKR set.
4. **Counter-metric that is itself gameable by the same volume
   strategy.** For example, pairing "PRs merged" with "LoC per PR" rewards
   both bloat and churn.
5. **Objective 3 replaced by a generic "team health" objective.** The
   anti-atrophy KR must bite; "everyone feels supported" is not a KR.
6. **Grading nomenclature unstated.** Calibration then compares
   "stretched" targets that mean different things on different teams and
   the exercise outcome degrades to theatre.
7. **No anti-scope section.** The OKR set silently deprioritises things
   without saying so, and the first surprise lands at end-quarter
   grading.
8. **A KR that is actually a project.** "Launch v2 of the API" belongs
   inside a KR as a milestone, not as an objective. A launch finishes;
   the OKR set describes the quarter after.
9. **Headcount ask smuggled into the KR targets.** "Hit X unless we get
   two more hires" is a planning document, not an OKR. Keep that in the
   Exercise 06 pre-read and link to it.
10. **Counter-metric gates stated as "we'll look at it".** A gate that is
    not numerical is not a gate. If the paired throughput KR is not
    zeroed by a specific counter-metric breach, the pairing is
    decorative.

## 6. References

Cite these three sources directly in the OKR set appendix; peer managers
in the same calibration group should be reading the same frameworks when
they grade each other's drafts.

- **NIST AI Risk Management Framework** — <https://www.nist.gov/itl/ai-risk-management-framework>
  Relevance: the AI RMF's **Measure** function is what the counter-metric
  gates in this OKR set are enacting. The **Manage** function is why the
  grading rule says a gate breach zeroes the paired throughput KR — a
  measured risk you refuse to act on is an unmanaged risk. The **Govern**
  function justifies Objective 3's attestation KR: a team that cannot
  attest who reviewed AI-generated code has not met Govern-level
  expectations, no matter how pretty the velocity numbers look.
  <!-- tbd-confirm: confirm the current function naming (Govern, Map,
  Measure, Manage) still applies in the active AI RMF revision at
  calibration time. -->
- **OWASP Machine Learning Security Top 10** — <https://owasp.org/www-project-machine-learning-security-top-10/>
  Relevance: sources the **scenario catalogue** for the Objective 3.2
  drills (prompt injection, context poisoning, output-integrity failures
  on model-assisted code). The OWASP list is the easiest-to-share
  starting inventory for a drill schedule; use it to seed drills and
  retire scenarios as the team's review-catch rate on them stabilises.
  <!-- tbd-confirm: confirm which items on the current OWASP ML Top 10
  revision apply directly to code-assistant workflows vs. production ML
  inference. -->
- **MITRE ATLAS** — <https://atlas.mitre.org/>
  Relevance: provides the **adversary-behaviour taxonomy** behind the
  escaped-AI-defect counting in KR 3.1 and the drill design in KR 3.2.
  ATLAS lets the team tag incidents with a shared vocabulary ("hallucinated
  dependency", "poisoned training data in upstream model", "prompt
  injection via tool output") so that the postmortem corpus aggregates
  cleanly over quarters and the OKR set can tighten its targets using
  real incident data rather than vibes.
  <!-- tbd-confirm: verify which ATLAS tactic/technique identifiers
  are most commonly cited in AI-code-assistant incident reports. -->
