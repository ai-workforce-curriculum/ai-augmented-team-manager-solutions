# Exercise 02 — Empirical Literature Read-and-Digest

> Module: `mod-104-productivity-measurement-and-ai-roi`
> Exercise type: **design** (worked reading digest + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is a **structured digest of the empirical productivity-and-AI literature** that a team manager can use to anchor internal claims. A well-done digest answers three questions, in order:

1. **What does the published evidence actually say** about AI coding assistants and developer productivity?
2. **Where does the evidence conflict**, and what are the conditions under which each finding seems to hold?
3. **Which claims can I therefore make inside my own organisation**, and which should I refuse to make — even if a vendor deck makes them?

A good digest is:

- **Honest about study design.** A controlled experiment on a canned task is not the same artefact as an observational within-firm rollout. A manager who conflates them will over-claim and lose credibility the first time a result does not reproduce internally.
- **Honest about effect sizes.** A paper reporting "55.8% faster completion" on a single task is not a licence to claim "AI makes developers 56% more productive." Effect sizes on constrained tasks rarely map to end-to-end throughput.
- **Honest about recency and generality.** Findings from 2022 Copilot studies may or may not generalise to 2026 agentic assistants. Call out the model, the task, the sample.
- **Scoped to what a team manager actually has to do with it.** The audience is not an academic reviewer — it is the person who has to decide which claim to put in a quarterly review.

Design decisions this solution encodes:

1. **A short digest beats an exhaustive bibliography.** Six to eight studies, each with a one-paragraph abstract-in-English, is more useful than a 40-paper spreadsheet nobody re-opens.
2. **Each study gets a *claim column* and an *internal-use column*.** The claim column records what the authors actually concluded; the internal-use column translates that into "what we are and are not allowed to say internally on the basis of this finding."
3. **Conflicts are called out, not papered over.** The digest flags where studies disagree (e.g., junior vs. senior benefit, controlled-task vs. observational effect, speed vs. quality).
4. **Every row names the study design.** RCT? Within-firm observational? Survey self-report? Benchmark on constrained tasks? The design determines the strength of the claim.
5. **A dedicated "claims we refuse to make" section.** Negative space is as important as positive findings — the digest exists partly to inoculate the team against vendor over-claiming.

## 2. Implementation — the digest

### 2.1 How to read this table

- **Design** codes the study type: `RCT` = randomised controlled trial; `Observational` = within-firm rollout with pre/post comparison; `Survey` = self-reported perception; `Benchmark` = fixed-task benchmark; `Mixed` = multiple of the above.
- **Headline claim** is the authors' own stated finding, paraphrased.
- **Internal-use translation** is what a team manager is justified in saying *inside their own organisation* based on this study — usually a weaker and more conditional statement than the headline.
- **Caveats** names what the study does *not* establish and the most load-bearing threat to generalisation.

### 2.2 The digest

| # | Study (short name) | Design | Headline claim | Internal-use translation | Key caveats |
|---|---|---|---|---|---|
| 1 | **Peng et al. 2023, "The Impact of AI on Developer Productivity"** (GitHub Copilot RCT) | RCT | Developers using Copilot completed a scripted HTTP-server task ~55.8% faster than controls. | "A specific, well-scoped coding task was completed meaningfully faster by developers using a 2022-era Copilot assistant. We cannot generalise this to our end-to-end lead time." | Single constrained task; 2022 model (ancestor of current assistants); completion time, not quality, is the dependent variable; no measurement of rework / defects. |
| 2 | **Microsoft / GitHub / Accenture rollout study (reported in GitHub Research, 2024)** | Observational + survey | Enterprise rollout of Copilot produced self-reported productivity increases and measurable upticks in PR counts. | "In an enterprise with mature tooling, our developers *perceive* higher productivity with the assistant on. PR-count movement is suggestive but confounded by rollout timing." | Within-firm; no counterfactual; perception bias; PR count is Activity (SPACE warns against reading Activity alone). Confirm the exact Accenture study citation, sample size, and study window before quoting percentages externally. |
| 3 | **Ziegler et al. 2024, "Measuring GitHub Copilot's Impact on Productivity"** (CACM) | Mixed (telemetry + survey) | Acceptance rate of suggestions correlates with reported productivity; acceptance ≥ ~30% predicts strong perceived value. | "Developers who accept more suggestions report more value. This is a conditional-usage finding, not a causal one — people who like the tool use it more and report liking it more." | Correlational; reverse causality plausible; cohort is Copilot-using developers, not a general population. |
| 4 | **DORA State of DevOps 2024** | Survey (large-N) | AI tool use correlates with reported individual productivity; correlations with team-level throughput and stability are mixed or absent. | "AI adoption is associated with higher *individual* perceived productivity. It is not reliably associated with better team-level DORA metrics (lead time, CFR, deployment frequency), and may correlate negatively with some in some cohorts." | Self-reported; cross-sectional; mediating variables (team process maturity, platform quality) do heavy lifting. Cross-check the specific 2024 DORA report findings on AI × throughput before quoting the exact direction of the correlation. |
| 5 | **METR 2025 "Measuring AI Ability to Complete Long Software Tasks"** | Benchmark (task-length frontier) | Frontier AI systems' time-horizon for completing software tasks has grown rapidly (doubling on a recurring cadence on their benchmark suite). | "Frontier models' capability on multi-hour agentic coding tasks is improving fast, measured on a specific benchmark. This is an upper-bound capability claim, not a productivity claim about our team." | Benchmark artefact; selection of tasks matters; capability on a benchmark is not productivity in a messy codebase. |
| 6 | **GitHub Copilot Economic Impact / Developer Experience study (GitHub, 2023)** | Survey | Developers using Copilot report higher satisfaction, less frustration, more flow. | "Self-reported DevEx is higher among active users. This is a *satisfaction* signal, which matters, but it is not a throughput claim." | Survey; selection bias (users who kept using it are in the sample). |
| 7 | **DX / `getdx.com` benchmark publications on AI tool usage (2024–2025)** | Observational (cross-firm) | Firms with higher reported AI adoption show higher Core 4 metric movement. | "There is a cross-firm correlation between adoption and Core 4 movement. Our own team's movement remains to be measured." | Cross-sectional; firm-level confounders (platform maturity, culture) likely; vendor-published. Confirm the exact DX publication, year, and methodology before relying on specific percentile claims. |
| 8 | **Independent randomised study on senior-developer tasks (frontier-model era)** | RCT | On realistic, open-source maintenance tasks, experienced developers using a current-generation agentic assistant were measured to be *slower* than without it, despite self-reporting faster. | "Headline productivity gains do not necessarily hold for senior maintainers on legacy codebases, and self-report can disagree with measured time. Apply the finding by segmenting cohorts, not averaging." | One task domain (open-source maintenance); small N; model/tool choice may not match internal tool. Confirm the exact 2025 METR / independent senior-developer RCT citation before quoting specific numbers. |

### 2.3 What the collective evidence supports — and does not

**Supports (defensible internal claims):**

- AI coding assistants produce **measurable time savings on constrained, well-scoped coding tasks**, in both RCT and benchmark settings. (Studies 1, 5.)
- Users of AI coding assistants report **higher developer satisfaction and lower frustration** on average. (Studies 3, 4, 6.)
- **Adoption and usage metrics are not themselves productivity metrics** — they predict satisfaction more reliably than they predict throughput. (Studies 3, 4.)
- **Effects are heterogeneous by cohort.** Junior developers, experienced developers, greenfield work, and legacy-maintenance work do not move together. (Studies 1, 8.)

**Does not support (refuse to make these claims internally):**

- "AI makes developers X% more productive." Pointed benchmark effects do not transfer directly to end-to-end productivity.
- "Our team's lead-time improvement is because of the AI rollout." Without a counterfactual, this is narrative, not evidence.
- "If every engineer adopts the assistant, throughput will rise proportionally." Adoption-to-throughput is non-linear and cohort-dependent.
- "Satisfaction gains mean quality is fine." Quality (change failure rate, defect escape rate) must be measured separately.
- "The 2023 Copilot results generalise to 2026 agentic tools." Model generation, tool shape, and workflow integration differ substantially; re-measure.

### 2.4 Study-design cheat sheet (how to weight a new paper the team cites)

| Design | What it is good for | What it is not good for |
|---|---|---|
| **RCT on constrained task** | Bounding the *task-level ceiling* of the tool's effect. | Predicting end-to-end team throughput. |
| **Benchmark (fixed task set)** | Tracking capability over time; comparing models. | Predicting effect in any specific team's codebase. |
| **Within-firm observational** | Noticing whether rollout is *at least not harmful*; generating hypotheses. | Causal claims; cannot isolate the AI rollout from co-occurring changes. |
| **Survey / self-report** | Measuring perception, satisfaction, friction. | Measuring actual throughput; self-report drifts from measured time. |
| **Cross-firm correlational (vendor benchmark)** | Orienting on what "high adoption" firms look like. | Setting your team's targets. Confounded by firm-level selection. |

### 2.5 How to use this digest

1. **Before you cite a study in a team or exec meeting, open the row.** Read the headline claim *and* the internal-use translation. Use the translation.
2. **When a vendor deck cites a figure you do not recognise, check the design.** If the design is not disclosed in the deck, treat the figure as marketing until you can locate the underlying study.
3. **When you present your own internal data, name the design.** "This is a within-firm observational number; here are the confounders we cannot rule out." This is the single highest-leverage habit you can build.
4. **Refresh every two quarters.** Model generations change; findings from a year ago may or may not hold.

## 3. Validation steps

1. **Rebuild one row from the source.** Pick one row of section 2.2, open the primary source, and verify that the "headline claim" paraphrase is accurate and that the "caveats" are the ones the authors themselves acknowledge. If you cannot verify a row against a primary source, the row is not usable — delete it or flag it in the caveats column as unverified-pending-research.
2. **Dry-run against a real vendor claim.** Find one recent vendor-published productivity figure (from a blog post, a sales deck, or a conference talk). Use section 2.4 to classify its design. Use section 2.3 to decide whether you can repeat the claim internally.
3. **Peer check with one senior IC and one adjacent manager.** Hand them section 2.3 (the supports / does not support lists) with the sources stripped. Ask: "Which of these would you push back on?" The pushback points are the ones to re-source.
4. **Pilot usage.** For four weeks, commit to replacing *any* productivity claim you make in writing (Slack, review decks, a quarterly) with a citation to a row in this digest or a `<!-- no supporting evidence -->` marker. Count how often you reached for the marker. If it is frequent, your digest is doing its job — stop repeating unsourced claims.
5. **Check for recency drift.** For each row older than 18 months, decide: is this still load-bearing, or has a newer study superseded it? Mark superseded rows explicitly rather than deleting them silently.

Validation is **epistemic hygiene**: you are checking that the digest actually constrains what you say, not that it reads well in isolation.

## 4. Rubric

Use this to grade a submitted digest. Each criterion scores 0–3.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Study coverage** | < 3 sources | 3–5 sources | 6–8 sources, mix of designs | 6–8 sources, mix of designs, at least one dissenting / counter-example included |
| R2 | **Design labelling** | Design not stated | Named but inconsistent | Every row names the design | Every row names design + a one-line note on why that design limits the claim |
| R3 | **Headline vs. internal-use separation** | Collapsed into one column | Mentioned in prose | Two columns, consistently filled | Two columns + explicit "refuse to make" negative-space list |
| R4 | **Caveats** | Missing or generic ("some caveats apply") | 1 caveat per row | Specific caveats, named | Specific caveats + the single most load-bearing threat to generalisation per row |
| R5 | **Conflict handling** | Conflicts hidden | Conflicts named | Conflicts named + a hypothesis for the conditions each finding holds under | Conflicts + cohort / design / recency hypothesis + a plan to re-measure internally |
| R6 | **Primary-source traceability** | Studies named but no citations | Citations present but unverified | Every citation checked once against the primary source | Every citation checked + each unverified row is explicitly flagged with a specific, actionable follow-up |
| R7 | **Managerial utility** | Reads like an academic review | Ends with a vague "takeaway" | Ends with a usage protocol (how to cite, how to refresh) | Usage protocol + four-week pilot plan + refresh cadence |
| R8 | **Guardrail on over-claiming** | No negative-space list | Vague cautions | Specific "refuse to make" list | Specific refuse-list + a worked example of translating a vendor figure into the team's internal claim |

**Pass bar: 16/24.** Below that, iterate before publishing to the team.

## 5. Common mistakes

- **Collecting 40 papers and digesting none.** The deliverable is not a bibliography. If your digest does not fit on one screen, you have not digested yet.
- **Copy-pasting the abstract.** Abstracts are written to be interesting. Your digest exists to be useful — the internal-use translation does not live in the abstract.
- **Conflating "statistically significant" with "operationally meaningful."** A 3% speedup on a 20-minute task is not an enterprise-moving number, even if the p-value is low.
- **Ignoring effect-size heterogeneity across cohorts.** If a study measures juniors and seniors together and reports an average, the average hides the story. Pull the cohort breakdown or flag the row as unverified pending that breakdown.
- **Reading only vendor-published studies.** Vendors have an incentive; independent researchers have a different one. Both are useful, but a digest with only one source class is a digest with a known bias.
- **Letting the digest decay.** A row dated 2023 cited in 2026 against a 2026 tool is probably misleading. Stamp every row with the model/tool generation studied, not just the publication date.
- **Conflating adoption with productivity.** Daily active users is a *leading indicator* of satisfaction, not a productivity measure. Keep them in different columns.
- **Fabricating a plausible-sounding citation.** Any row you cannot open the primary source on belongs under an explicit "unverified — pending research" flag. A fake citation is worse than a missing one.

## 6. References

- Peng, S., Kalliamvakou, E., Cihon, P., & Demirer, M. (2023). *The Impact of AI on Developer Productivity: Evidence from GitHub Copilot.* arXiv:2302.06590. <https://arxiv.org/abs/2302.06590>
- Ziegler, A., Kalliamvakou, E., Simister, S., Sittampalam, G., Li, A., Rice, A., Rifkin, D., & Aftandilian, E. (2024). *Measuring GitHub Copilot's Impact on Productivity.* Communications of the ACM. <https://cacm.acm.org/research/measuring-github-copilots-impact-on-productivity/>
- Forsgren, N., Storey, M.-A., Maddila, C., Zimmermann, T., Houck, B., & Noda, J. (2021). *The SPACE of Developer Productivity.* ACM Queue, 19(1). <https://queue.acm.org/detail.cfm?id=3454124> — included because any claim about "productivity" the digest makes is measured in a SPACE-aware way.
- **DORA State of DevOps Report** — the annual report published at `dora.dev`; the AI / productivity cross-tab is the relevant section. Confirm the specific year (2024 vs. 2025) of the AI × throughput finding before citing externally.
- METR. *Measuring AI Ability to Complete Long Software Tasks.* (2025). <https://metr.org/> — benchmark publication on model capability time-horizon on software tasks.
- DX / `getdx.com` — the DX Core 4 write-up and cross-firm benchmarking reports; relevant for the "adoption correlates with Core 4 movement" row. <https://getdx.com/>
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the Measure function requires study-design-aware interpretation of system-performance claims; the digest is this exercise's operationalisation of that requirement.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) and [MITRE ATLAS](https://atlas.mitre.org/) — referenced when a productivity claim intersects a risk claim (e.g., velocity gained at the cost of a security regression); productivity figures should not be reported without the counter-metric.

<!-- follow-up: after one quarter of using the digest to gate internal claims, add a column recording how often each row was cited and prune / refresh rows that were never used. -->
