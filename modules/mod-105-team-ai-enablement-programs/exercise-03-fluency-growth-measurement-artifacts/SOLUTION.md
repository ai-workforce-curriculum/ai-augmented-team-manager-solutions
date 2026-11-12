# Exercise 03 — The Fluency-Growth Measurement Artifact Set

> Module: `mod-105-team-ai-enablement-programs`
> Exercise type: **implementation** (runnable artifact set + decision rationale + grading rubric)

## 1. Solution overview

A team manager running an enablement program needs to answer a specific, deceptively hard question at the end of a quarter: *did the team's AI fluency actually grow?* Self-reported fluency is biased upward by novelty. Tool-usage counters measure traffic, not skill. One-off demos cherry-pick. The honest answer requires a **set** of artifacts that triangulate — each weak on its own, but together producing a defensible signal.

The deliverable is a four-artifact set the manager can run every quarter:

1. **A baseline assessment** (Day 0) — a short structured self-and-manager assessment on six fluency dimensions.
2. **A rolling fluency journal** — each IC's lightweight log of AI-assisted work and the mistakes caught.
3. **A quarter-end assessment** (Day 90) — the baseline rerun, with the same questions, same scale, same scoring.
4. **A dashboard** — four indicators the manager posts in the team wiki, derived from the above plus usage telemetry.

Design decisions this solution encodes:

1. **Measure *fluency*, not *productivity*.** Productivity measurement belongs to mod-104 (DX Core 4 / SPACE). This artifact set measures whether people are getting *better at using assistants safely and effectively*. The two correlate but are not the same, and conflating them produces bad decisions.
2. **Same instrument before and after.** Self-reported "I got better" is cheap. A Day 0 baseline with the same questions, rerun verbatim at Day 90, is the single most important design choice in the whole artifact set — without it, the comparison is nothing.
3. **Fluency has multiple dimensions.** We measure six: (a) *task framing*, (b) *verification*, (c) *failure-mode awareness*, (d) *tool selection*, (e) *scope control*, and (f) *disclosure and attestation*. A single scalar collapses too much.
4. **Qualitative evidence is required, not optional.** Every self-score ≥ 2 must be backed by a specific artifact — a PR, a doc, a journal entry. Unbacked scores are reset to 1.
5. **The manager scores independently.** The manager scores the same IC on the same dimensions. The gap between self-score and manager-score is itself data — it indicates calibration, not disagreement to resolve.
6. **Dashboard shows direction, not level.** The team-wiki dashboard reports *change since baseline*, not raw scores, to keep the team focused on learning rather than status ranking.

## 2. Worked implementation — the artifacts

### 2.1 Artifact A — Baseline / quarter-end assessment form

Save as `fluency-assessment-v1.md`. Fill out once at Day 0 and again at Day 90. Do **not** change wording between runs.

```markdown
# AI Fluency Assessment — <IC name>, <quarter>

> This form is scored twice per quarter: once by the IC (self) and once
> by the manager. Each item scores 0–3.
>
> - 0 — No experience / have not tried.
> - 1 — Can do it with help; would struggle unsupervised.
> - 2 — Can do it unsupervised on routine work; can point to one
>       concrete artifact in the last 30 days.
> - 3 — Can do it unsupervised on non-routine work; can point to two
>       concrete artifacts in the last 30 days AND has helped another
>       IC do it.
>
> Any score ≥ 2 that is not backed by a concrete artifact drops to 1.

## F1. Task framing
Can you take a fuzzy ticket or ask and reduce it to a prompt the
assistant can act on — with scope, constraints, and acceptance
criteria?
- Self score (0–3): __  Evidence: __________________________
- Manager score (0–3): __  Rationale: ______________________

## F2. Verification
When the assistant produces output, do you verify it against primary
sources (docs, source, data) rather than accepting the completion?
- Self score (0–3): __  Evidence: __________________________
- Manager score (0–3): __  Rationale: ______________________

## F3. Failure-mode awareness
Can you name the assistant's typical failure modes in your stack and
describe a specific instance you caught in the last 30 days?
- Self score (0–3): __  Evidence: __________________________
- Manager score (0–3): __  Rationale: ______________________

## F4. Tool selection
Can you choose between the tools in the team's portfolio for a given
task, and justify the choice in two sentences?
- Self score (0–3): __  Evidence: __________________________
- Manager score (0–3): __  Rationale: ______________________

## F5. Scope control
Can you keep an AI-assisted change bounded — prevent scope creep,
limit the diff, and keep refactors separate from feature work?
- Self score (0–3): __  Evidence: __________________________
- Manager score (0–3): __  Rationale: ______________________

## F6. Disclosure and attestation
Do you disclose AI use in PR descriptions and documents per the team
policy, and tick the review checklist's attestation items honestly?
- Self score (0–3): __  Evidence: __________________________
- Manager score (0–3): __  Rationale: ______________________

## Totals
- Self total (0–18): __
- Manager total (0–18): __
- Self–manager gap (self − manager): __

## Narrative (both scorers, 2–3 sentences each)
- Self: what you would like to improve on next quarter: __________
- Manager: one dimension to invest in next quarter: ______________
```

### 2.2 Artifact B — Rolling fluency journal

Save as `.ai-journal.md` in each IC's personal notes or team wiki page. Append-only. Expected cadence: one entry per AI-assisted PR or significant AI-assisted document.

```markdown
# AI Journal — <IC name>

## Entry template
### <date> — <short description>
- **Artifact**: link to PR, doc, or output.
- **Tool and mode**: e.g. "Claude Code, agent mode" / "GitHub Copilot
  inline" / "ChatGPT voice, brainstorm only".
- **What I asked for**: one sentence.
- **What the assistant produced**: one sentence.
- **What was wrong with it, if anything**: specific. "Hallucinated an
  API method `UserService.deleteSoft()` that doesn't exist" beats
  "some issues".
- **What I kept / rewrote / threw away**: with proportions, roughly.
- **What I learned that I didn't know before this entry**: one line.
  If nothing, write "nothing new".
- **Minutes spent reviewing the output**: estimate.

## Monthly reflection (last day of each month)
- Top three recurring failure modes I caught this month:
- One prompting or verification habit I want to change next month:
- One question I want to raise with the team at the next assist demo:
```

### 2.3 Artifact C — Team dashboard

Save as a wiki page titled "AI fluency — <quarter> dashboard." The manager updates it monthly. All numbers are *relative to baseline*; no raw scores are posted per-person.

```markdown
# AI Fluency Dashboard — <team>, <quarter>

Owner: <manager>. Updated monthly. All figures are relative to Day 0
  baseline unless stated. No individual raw scores are published.

## Indicator 1 — Fluency trajectory (manager-scored)
Sum of manager F1–F6 totals across all ICs, change vs. baseline.
- Baseline total: ___
- Current total: ___
- Change: + __  ( direction: ↑ / ↓ / flat )

## Indicator 2 — Dimension that grew most / least (team-level)
Which of F1–F6 shifted most in aggregate, and which shifted least.
- Most growth: __  (+ __ points across the team)
- Least growth: __  (± __ points across the team)

## Indicator 3 — Self-manager calibration
Average |self − manager| gap across the team.
- Baseline average gap: ___
- Current average gap: ___
- Direction: shrinking (good) / stable / widening (investigate)

## Indicator 4 — Journal cadence
Fraction of ICs with ≥ one journal entry per two weeks on average.
- Baseline (Day 30): __ %
- Current: __ %

## Qualitative highlights (3 bullets)
- Named failure modes caught this month:
- One specific habit change observed:
- One workflow experiment update (see Exercise 01, Phase 2):
```

### 2.4 Artifact D — Review protocol

Save as `fluency-measurement-protocol.md` in the team wiki. This is the manager's playbook.

```markdown
# Fluency Measurement Protocol

## Timing
- Day 0: baseline assessment (self + manager) for every IC.
- Day 15 and Day 45: lightweight pulse (3 questions, see Exercise 01).
- Day 60: mid-quarter checkpoint — review journals for cadence and
  depth, flag any ICs with zero entries.
- Day 90: quarter-end assessment (self + manager), same form,
  verbatim.
- Day 90 + 5: dashboard update + 1:1s to discuss individual deltas.

## Scoring rules
- Any self-score ≥ 2 without an evidence link drops to 1.
- Manager scores independently and records rationale.
- The self–manager gap is not an argument to resolve; it is an input
  into coaching. Narrow a growing gap by surfacing shared artifacts.

## Privacy
- Individual raw scores are shared only between the IC and the
  manager, and in a 1:1. Team-level dashboard shows aggregates and
  deltas only.
- The journal is owned by the IC. The manager may review entries
  only with the IC's consent (default: review together in 1:1).

## Decision gates
- If team-level indicator 1 is flat or down at Day 90, review the
  enablement plan's Phase 2 workflow experiment. Fluency should move
  before productivity does; if neither moved, the plan needs
  revision.
- If calibration gap (indicator 3) widens, it usually means either
  (a) ICs are over-estimating fluency, or (b) the manager is
  under-exposed to IC work. Both are actionable.
```

### 2.5 Decision rationale (why these artifacts and not others)

- **Six dimensions, not one scalar or twenty items.** A single "AI fluency" number is uninterpretable. Twenty items collapses from fatigue after two rounds. Six is small enough to answer in 10 minutes and large enough to distinguish the dimensions ICs actually vary on.
- **Baseline is non-negotiable.** Without Day 0 scores, every Day 90 claim about growth is unfalsifiable. The baseline is the single most important artifact in the set.
- **Evidence-backed scoring.** "I think I'm a 3" is noise. "I'm a 3 and here's the PR" is signal. The rule that unbacked ≥ 2 scores drop to 1 kills the ceiling-rush pattern that one-shot self-assessments produce.
- **Manager scores independently.** This is the governance layer. The manager must *see* the evidence and score it, not rubber-stamp the self-score. The gap is coaching material, not a dispute.
- **Journal cadence as an indicator in its own right.** An IC with zero journal entries isn't necessarily un-fluent, but they are *untraceable* — the manager has no primary source to score from. Journal cadence indirectly measures whether the enablement program is producing observable practice at all.
- **No individual raw scores on the dashboard.** The dashboard is a *team learning* instrument, not a status ranking. Publishing individual scores converts the whole set into a competitive instrument, which is corrosive for exactly the dimensions we are trying to grow (F6 honest attestation, F3 failure-mode admission).
- **Separation from productivity.** mod-104 covers productivity measurement (DX Core 4, SPACE). This artifact set measures *fluency*. Teams that measure fluency with productivity metrics end up optimising throughput and missing the quality and safety dimensions.
- **Alignment with NIST AI RMF.** The scoring and evidence requirements map to the **Measure** function (specifically **Measure 1.1**: approaches and metrics to measure AI risks are identified and applied) and the governance of measurement to **Govern 4.3** (organizational practices are in place to enable AI testing, identification of incidents, and information sharing).

## 3. Validation steps

1. **Dry-run the baseline on yourself first.** Score yourself on F1–F6, then ask a peer manager to score you. If the two scorers cannot agree on what a "3" looks like on any item, tighten the item's wording before giving it to the team.
2. **Verify the "unbacked ≥ 2 drops to 1" rule bites.** Pilot the assessment on one IC. If every self-score comes through unchanged, your evidence rule is not being enforced. Spot-check two of the evidence links and confirm they support the score.
3. **Check the journal template on a real PR.** Fill out a journal entry for one of your own assisted PRs this week. If the template felt onerous, trim it. If it felt empty, add the missing field.
4. **Dashboard dry-run.** Produce the dashboard from the baseline alone (so indicators are "flat"). Confirm it is readable and interesting at team-sync without revealing individual scores.
5. **Decision-gate rehearsal.** Imagine the Day 90 totals are flat and the calibration gap has widened. Walk through the protocol: what do you do, and in what order? If you cannot describe the follow-up, the protocol is missing a step.
6. **One-quarter pilot.** Run the whole set for one quarter. The most useful signal in that quarter is not the fluency delta — it is which items IC's push back on and which the manager cannot score honestly. Revise before the next quarter.

## 4. Rubric

Use this to grade a submitted artifact set (self-assessment or peer review). Each criterion scores 0–3.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Dimensions covered** | 1 scalar or ad-hoc list | 3–4 dimensions | 5–6 dimensions distinct | 6 dimensions covering framing, verification, failure-modes, tool-selection, scope, disclosure |
| R2 | **Baseline / rerun discipline** | No baseline | Different instrument at end | Same instrument, loose wording | Verbatim same instrument, pre-registered |
| R3 | **Evidence requirement** | None | "Give an example" optional | Evidence required for high scores | Unbacked ≥ 2 explicitly drops to 1, with manager audit |
| R4 | **Manager-scored layer** | Self-score only | Manager spot-checks | Manager scores independently | Manager scores + rationale + gap used as coaching input |
| R5 | **Journal design** | None | Free-text diary | Structured template | Structured template + monthly reflection + consent-based review |
| R6 | **Dashboard design** | Individual raw scores posted | Aggregate total only | Aggregate + direction | Aggregate + direction + calibration-gap + cadence + qualitative highlights |
| R7 | **Protocol timing** | None | Only start and end | Pulses between | Day 0 + Day 15 + Day 45 + Day 60 + Day 90 + Day 90 + 5 |
| R8 | **Privacy and scope** | Public raw scores | Shared freely | Individual scores private to manager + IC | Explicit policy + IC owns journal + aggregates only on dashboard |
| R9 | **Decision gates** | None | "We'll review later" | One decision gate | Decision gate tied to Phase 2 experiment and Phase 3 memo |
| R10 | **Separation from productivity metrics** | Conflates with DX Core 4 | Mentions distinction | Clear scope boundary | Clear boundary + cross-ref to mod-104 for productivity, mod-107 for atrophy |

**Pass bar: 20/30.** Below that, iterate before issuing to the team.

## 5. Common mistakes

- **Collapsing fluency to one number.** A scalar is easy to publish and impossible to act on. Six dimensions are harder to publish and much easier to act on.
- **Changing the instrument between baseline and rerun.** This is the single most common failure. Any wording drift invalidates the comparison. Freeze the form at Day 0 and do not touch it.
- **Letting self-scores run unbacked.** Unbacked ≥ 2 scores produce ceiling effects that look like growth but are actually enthusiasm. The drop-to-1 rule looks harsh and is essential.
- **Treating the self–manager gap as a dispute to resolve.** The gap is *data*. Flattening it by negotiation destroys the information. Use it to drive the 1:1.
- **Posting individual raw scores to the team.** The dashboard converts into a status ranking the moment it has names and numbers. Aggregates and deltas only.
- **Measuring fluency with productivity tools.** Lines-of-code, PR counts, commit volume — these are productivity signals, and they are already served by DX Core 4 / SPACE (mod-104). Mixing them in here makes the fluency instrument noisy and the productivity instrument biased.
- **Skipping the journal because "it's a chore."** The journal is the only artifact that captures *what the assistant got wrong*. Dropping it leaves the manager with scores but no primary sources.
- **Making the journal mandatory per-PR.** Mandatory-per-PR produces hollow entries. "Per AI-assisted PR" + a monthly reflection is the sweet spot for cadence.
- **Running the measurement once and never again.** The point of baseline + rerun is to establish a recurring rhythm. One run is a vanity exercise.
- **Not wiring the decision gate.** A dashboard with no follow-up protocol is a vanity dashboard. Indicator 1 flat at Day 90 must trigger a specific review.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the **Measure** function (identifying and applying metrics for AI risk) and **Govern 4.3** (testing, incident identification, information sharing) underpin the scoring and evidence requirements of the artifact set.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — supplies the failure-mode framing for the F3 dimension (teams score higher when they can name AI-specific security failure modes, not just generic "hallucination").
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat taxonomy used to sanity-check that the F3 failure-mode dimension covers the techniques most likely to apply in the team's actual workflow.

<!-- follow-up: once two quarters of baseline-to-rerun data exist on the same team, replace the "evidence required for ≥ 2" heuristic with a calibration curve from the observed self-vs-manager gap at each score band. Until then, the drop-to-1 rule is a reasonable default, not a cited calibration. -->
