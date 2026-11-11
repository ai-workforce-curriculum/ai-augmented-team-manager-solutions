# Exercise 01 — DX Core 4 and SPACE, Side by Side

> Module: `mod-104-productivity-measurement-and-ai-roi`
> Exercise type: **design** (worked comparison + decision rationale + rubric)

## 1. Solution overview

The deliverable is a one-page **side-by-side mapping of DX Core 4 and SPACE** that a team manager can hand to their director, their TPM, or a skeptical finance partner and say: *"Here is what each framework measures, here is what it does not, here is which of our open questions it actually answers."* The audience is not academic — it is the person deciding which dashboard to fund.

A good comparison is:

- **Framed by decisions, not by metric lists.** The reader cares which framework *answers their question*, not which has more dimensions.
- **Honest about overlap.** DX Core 4 is explicitly a *reconciliation* of DORA + SPACE + DevEx; pretending it is a disjoint alternative misleads.
- **Explicit about the AI-specific gap both frameworks share.** Neither was designed to attribute productivity movement to AI tool rollout; a team measuring AI ROI will have to extend one of them, not pick between them.
- **Scoped to team-manager authority.** A team manager cannot ship a company-wide KPI; they can ship a dashboard on their own team. The comparison should surface metrics that are *instrumentable at team scope*.

The core design decisions this solution encodes:

1. **SPACE is a thinking framework; DX Core 4 is an answer sheet.** SPACE (Forsgren et al., ACM Queue 2021) argues productivity is multi-dimensional and cannot be reduced to one number — it names five dimensions (Satisfaction & well-being, Performance, Activity, Communication & collaboration, Efficiency & flow) and insists you draw from at least three. DX Core 4 (Tacho & Noda, DX / `getdx.com`, 2024) prescribes four concrete top-level metrics (Diffs per engineer, DevEx score, Change failure rate, Speed-of-impact) that already satisfy the "cross three SPACE dimensions" test. One is a meta-framework; the other is a specific instantiation.
2. **Neither framework gives you AI attribution out of the box.** Both predate the broad rollout of coding assistants. Measuring AI ROI requires an *extension* (usage + adoption + retention data, cross-cut with the chosen productivity metrics and segmented by cohort or before/after).
3. **At team scope, prefer DX Core 4 as the default and SPACE as the scoping lens.** A team manager has bandwidth for ~4 top-line metrics and a quarterly qualitative survey. DX Core 4 fits. Reach for SPACE when a specific question (e.g., "is our on-call pager load killing throughput?") is not answered by the Core 4.

## 2. Implementation — the side-by-side

### 2.1 What each framework actually is

| | **SPACE** | **DX Core 4** |
|---|---|---|
| Origin | Forsgren, Storey, Maddila, Zimmermann, Houck, Noda. *The SPACE of Developer Productivity* (ACM Queue, 2021). | Laura Tacho, Abi Noda, and the DX research team. Public write-up on `getdx.com`, 2024. |
| Shape | **Framework** — five dimensions, select metrics from each. | **Fixed set** — four top-line metrics with defined sub-indicators. |
| Prescriptive? | No. Picks dimensions; you pick metrics. | Yes. Specifies the four numbers. |
| Explicit goal | Replace the one-metric-to-rule-them-all habit; measure across levels (individual / team / org) with ≥3 dimensions. | Reconcile DORA + SPACE + DevEx into a small set a leader can run. |
| Collection | Mix of system telemetry, surveys, self-report. | Mix of telemetry and a periodic DevEx survey (the "D" in Core 4). |
| Where it lives in the ACM / vendor ecosystem | Peer-reviewed article in ACM Queue. | Vendor-published framework (DX) grounded in prior research including SPACE and DORA. |

### 2.2 Dimension / metric map

| SPACE dimension | What SPACE says to look at (illustrative, per the ACM Queue paper) | Which DX Core 4 metric covers it | Gap at team scope |
|---|---|---|---|
| **S — Satisfaction & well-being** | Developer satisfaction, retention, on-call burnout, perceived productivity | **DevEx score** (survey-based perception index) | Usually under-measured; teams skip the survey. The gap is *qualitative*. |
| **P — Performance** | Outcome quality: reliability, customer impact, defect rate | **Change failure rate** (DORA heritage) + **Speed-of-impact** on the outcome side | Attributing an outage to a change takes tooling. Needs a deploy-tracking signal. |
| **A — Activity** | Volume: counts of PRs, commits, reviews, deploys | **Diffs per engineer** (PRs merged / eng / week) | Easiest to collect. Easiest to gameable. Only interpretable alongside DevEx + CFR. |
| **C — Communication & collaboration** | Review quality, knowledge sharing, meeting load | **DevEx score** has sub-items here (e.g., ease of getting help, review latency) | No single Core 4 number; falls into the survey. Important for distributed teams. |
| **E — Efficiency & flow** | Lead time, deep-work time, context switches, interruption count | **Speed-of-impact** (time from first commit to customer value) + DevEx sub-items on flow | Instrumenting "deep work" is hard. Most teams proxy it with meeting-load counts. |

### 2.3 What each framework answers well

| Question a team manager asks | SPACE | DX Core 4 |
|---|---|---|
| "Are we shipping faster than six months ago?" | Yes, if you picked Activity + Efficiency metrics. | Yes — Diffs per engineer + Speed-of-impact. |
| "Are we shipping *worse*?" | Yes, via the Performance dimension. | Yes — Change failure rate. |
| "Is my team burning out?" | Yes, via Satisfaction & well-being. | Partially — DevEx captures satisfaction but not necessarily burnout; may need to add a burnout-specific sub-item. |
| "Is Copilot / Claude Code actually helping?" | No — SPACE pre-dates the coding-assistant wave; you have to design the AI-attribution overlay yourself. | No — same gap. Core 4 was not published as an AI-ROI framework. |
| "Where should I *cut* process overhead?" | Yes, via Efficiency & flow + Communication. | Partially — DevEx items on friction catch it; no single number. |
| "How does my team compare to peers?" | No — SPACE deliberately does not benchmark. | Partially — DX publishes benchmarks for Core 4 metrics, but treat them as orientation rather than targets (see Common mistakes). |

### 2.4 Where they disagree

- **On "a single number."** DX Core 4 is willing to present four numbers to a leader and let them triangulate. SPACE will not let you collapse to even one dimension; it insists on three *dimensions* (plural). In practice this matters only if an exec demands a single traffic-light.
- **On Activity.** SPACE warns, in writing, that Activity in isolation is misleading. DX Core 4 includes Diffs per engineer as one of the four top-line numbers *because* it is paired with CFR and DevEx — the pairing is load-bearing. Reporting Diffs per engineer alone is a misuse of either framework.
- **On the role of survey data.** SPACE treats self-report as a first-class signal. DX Core 4 operationalises this with the DevEx score. A team manager who refuses to field a survey has effectively opted out of both frameworks' Satisfaction leg.

### 2.5 Recommendation at team scope

For a team of 5–15 engineers whose manager is accountable for throughput, quality, and AI tool adoption:

1. **Adopt DX Core 4 as the default dashboard.** It is small, defensible, and already satisfies SPACE's "across ≥3 dimensions" discipline.
2. **Use SPACE as the *scoping lens* when a question is not answered by the four.** When someone asks "why are PRs going out faster but reviewers complaining?" — the answer is in SPACE's Communication dimension, which the Core 4 handles via DevEx sub-items but not via a top-line number.
3. **Add an AI-attribution overlay on top.** AI tool usage (daily actives, accepted suggestions, task time-to-done on AI-assisted tasks) is *not* a productivity metric; it is an *input* that you cross-reference with the Core 4. See Exercise 03 (token-cost tracking) and Exercise 04 (the ROI story).
4. **Field the DevEx survey quarterly, not annually.** Teams drift on friction faster than annual cadence catches. Quarterly is the minimum for the signal to be acted on.
5. **Never present a single Core 4 metric without its counter-metric.** Diffs per engineer alone invites gaming. Diffs per engineer with CFR and DevEx is defensible.

## 3. Validation steps

To validate the side-by-side against your team's real situation:

1. **List your three loudest open questions about team productivity.** For each one, answer: which SPACE dimension does it map to? Which DX Core 4 metric answers it? If two of your three questions do not have an answer in either framework, you have found the extension work that needs to happen.
2. **Pull one month of your own team's data.** Count merged PRs per engineer, change failure rate, time from first commit to deploy. If any of these three is more than a day's work to produce, you have an instrumentation gap, not a framework gap — fix the pipeline before arguing frameworks.
3. **Run a 10-minute DevEx pulse.** Ask your team the three DevEx sub-items most likely to move this quarter (e.g., *How easy is it to get a review?* / *How often did deep-work time get interrupted?* / *How confident are you in our tests?*). If the pulse surfaces a problem the four top-line numbers do not, you have confirmed the SPACE-as-scoping-lens claim empirically.
4. **Dry-run the comparison with a skeptical reader.** Ask a peer manager or director to redline the side-by-side. If they finish it saying "I still don't know which one to use," rewrite section 2.5.
5. **Check the AI gap.** Before you report any productivity delta, ask: "Could this delta be explained by *anything other than* the AI tool rollout?" (New hires, team re-org, migration finishing, holiday compression, re-platform.) If you cannot name at least three plausible confounders, you are not yet measuring ROI — you are telling a story. Return to Exercise 04.

Validation is **operational**: you are checking that the mapping changes what your team actually measures and how they interpret deltas, not that the comparison reads cleanly.

## 4. Rubric

Use this to grade a submitted DX Core 4 / SPACE side-by-side. Each criterion scores 0–3.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Accurate framing of each framework** | SPACE and DX Core 4 confused or treated as equivalents | Names each correctly but no origin / shape detail | Origins cited + prescriptive-vs-framework distinction | Origins cited + prescriptive-vs-framework + relationship (DX Core 4 reconciles DORA + SPACE + DevEx) |
| R2 | **Dimension / metric map** | Missing or cherry-picked | Covers 3 of the 5 SPACE dimensions | All 5 SPACE dimensions, mapped to Core 4 metrics | All 5 dimensions + named gaps where Core 4 does not cover + at-team-scope caveats |
| R3 | **Decision framing** | Metric list only, no decisions | Single generic use case | 3+ decisions mapped to framework(s) | 5+ decisions + explicit note of the AI-ROI gap in both |
| R4 | **Activity-metric handling** | Reports Diffs per engineer alone | Pairs with one counter-metric | Pairs Diffs with CFR *and* DevEx | Pairs Diffs with CFR + DevEx + explicitly cites SPACE's warning against Activity-in-isolation |
| R5 | **Survey / qualitative leg** | Omitted | Acknowledged | DevEx survey operationalised with a cadence | DevEx cadence + specific sub-items + how results feed back into the dashboard |
| R6 | **AI-attribution gap** | Not mentioned | Mentioned vaguely | Named as an extension on top of the chosen framework | Named + sketched (usage × productivity delta, with confounder-naming requirement) |
| R7 | **Recommendation at team scope** | None, or "use both" | One framework picked, no rationale | Picked framework + cadence + survey | Picked framework + cadence + survey + explicit "don't ship a single metric alone" rule |
| R8 | **Fit to real context** | Generic template, no specifics | One team detail | Multiple references to the team's stack, size, or pain points | The comparison is unambiguously about *this* team and its decisions |

**Pass bar: 16/24.** Below that, iterate before publishing to the team.

## 5. Common mistakes

- **Treating SPACE and DX Core 4 as competitors.** They are not. DX Core 4 is one well-formed instantiation of SPACE's "measure across ≥3 dimensions" discipline, with DORA's reliability leg folded in. The useful question is not *which* but *what gaps remain after you adopt Core 4 as the default*.
- **Reducing to a single number anyway.** Executives will ask for one traffic-light. Agreeing to one defeats the point. Offer two: a throughput number (Diffs per eng) **and** a counter-metric (CFR). If forced to pick one, pick CFR — reliability regressions are harder to reverse than throughput dips.
- **Skipping the DevEx survey because "surveys are soft."** The S in SPACE and the D in Core 4 both rest on self-report. Removing it is not rigor; it is a measurement regression.
- **Reporting Activity metrics without their counter-metrics.** Diffs per engineer alone is both frameworks' canonical anti-pattern. SPACE names this explicitly in the Queue article.
- **Expecting either framework to measure AI ROI.** Neither does, by itself. AI usage, acceptance, and retention data are *inputs*, not productivity metrics. Cross-cutting those with Core 4 is the team manager's job, not the framework's.
- **Benchmarking against public medians without matching context.** A team of 4 platform engineers and a team of 40 front-end engineers do not share a baseline. DX publishes benchmarks; read them as *orientation*, not as targets, and confirm the underlying report's sampling methodology before citing its percentiles externally.
- **Publishing the dashboard without a review cadence.** A metrics dashboard without a scheduled "what did we learn and what are we changing?" meeting is theatre. Monthly team-manager review, quarterly for the broader stakeholder set.
- **Treating the mapping as settled.** Both frameworks are evolving (SPACE is five years old; DX Core 4 is ~2024). Expect the dimension / metric map to need refresh at least yearly.

## 6. References

- Forsgren, N., Storey, M.-A., Maddila, C., Zimmermann, T., Houck, B., & Noda, J. (2021). *The SPACE of Developer Productivity: There's more to it than you think.* ACM Queue, 19(1). <https://queue.acm.org/detail.cfm?id=3454124> — the source article defining the five SPACE dimensions and the "at least three dimensions, at least two levels" discipline.
- Tacho, L. & Noda, A. (2024). *DX Core 4: a unified framework for engineering productivity.* DX / `getdx.com`. <https://getdx.com/research/measuring-developer-productivity-with-the-dx-core-4/> — the source write-up for the four top-line metrics (Diffs per engineer, DevEx score, Change failure rate, Speed-of-impact).
- Forsgren, N., Humble, J., & Kim, G. *Accelerate* (IT Revolution, 2018) and the annual **DORA State of DevOps** reports (`dora.dev`) — the origin of the four DORA metrics (deployment frequency, lead time for changes, change failure rate, time to restore service) that Core 4's CFR and Speed-of-impact inherit from.
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — supplies the Measure function's requirement to track AI system performance against defined metrics; useful when extending the Core 4 with an AI-attribution overlay.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) and [MITRE ATLAS](https://atlas.mitre.org/) — not productivity frameworks, but referenced when the AI-ROI overlay must also account for the risk cost (incidents, regressions) of the AI rollout, not just the velocity gain.

<!-- follow-up: after one quarter of running Core 4 + AI overlay, replace section 2.3 with the three questions your dashboard *actually* answered and the three it did not. -->
