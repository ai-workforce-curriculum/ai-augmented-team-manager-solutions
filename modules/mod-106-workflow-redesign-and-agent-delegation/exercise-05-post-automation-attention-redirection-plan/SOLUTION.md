# Exercise 05 — Post-Automation Attention Redirection Plan

> Module: `mod-106-workflow-redesign-and-agent-delegation`
> Exercise type: **design** (worked plan + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is a short, team-owned **attention-redirection plan**: for every workflow the team has automated or delegated to agents in this module, the plan names (a) the hours each team member is likely to *get back*, (b) where the team has decided those hours should go, (c) how the redirection will be measured, and (d) what will be done if the redirection doesn't happen.

This exercise exists because the hardest failure mode of AI-assisted delegation on a team is not a wrong merge — it's **the slow conversion of freed-up hours into busywork, meetings, or ambient Slack overhead.** Without a plan, "AI saved us a day a week" quietly becomes "we fit more half-finished work into the same week." The plan is the counter-pressure.

A good plan is:

- **Written against concrete workflows**, with concrete time estimates. "AI saves time on code review" is not a plan; "the single-file-refactor flow dropped from ~45 min to ~10 min, saving ~1h/week/IC" is.
- **Explicit about where the hours go.** The team picks a *small number* of named destinations and commits to them, rather than letting the hours diffuse.
- **Measurable without inventing a new instrumentation stack.** Prefer existing signals (PRs merged per week, ADRs written, mentoring sessions logged, on-call toil tracked, training hours logged) over bespoke tracking.
- **Signed by the team manager.** The manager is the party whose job it is to defend the newly-opened hours from being quietly re-consumed by other stakeholders.
- **Revisited quarterly.** The savings estimate is almost always wrong on first draft; the plan must be able to correct.

Design decisions this solution encodes:

1. **Attention is the scarce resource, not time.** Saving 3h/week of "I would have done this low-attention task" is not the same as 3h of deep-focus work. The plan names the *kind* of attention freed up, not just the hour count.
2. **A small number of destinations, named.** Pick 2–4 destinations per IC. Diffusion ("just use the time however") is the failure mode.
3. **Default destinations are the ones the team has historically under-invested in.** Mentoring, ADR writing, eval-suite maintenance, incident drills, tech debt with written-down intent, technical learning for anti-atrophy. These are the areas AI delegation makes *more* important, not less (see Mod-103 Ex-03, Mod-109, Mod-107 for the ones this curriculum covers).
4. **The plan explicitly refuses certain redirections.** "More tickets per sprint" is refused. "More meetings" is refused. "More Slack responsiveness" is refused. Naming the refusals is as important as naming the targets.
5. **Measurement is behavioural, not productivity-theatre.** The plan tracks *did the destination actually get the hours?* via existing signals, not via self-reported timesheets.
6. **The plan is a contract between manager and team, not a stakeholder commitment.** It is not a commitment to deliver more output to the business.
7. **It plans its own failure.** If the hours do not appear, or they appear and go somewhere other than the named destinations, the plan names what the team will do about it.

## 2. Implementation — worked plan

### 2.1 Scope statement (printed at the top)

```markdown
# Post-Automation Attention Redirection Plan — v0.1

**Team:** <team name>
**Owner:** <team manager name>
**Last revised:** 2026-11-11
**Next scheduled revision:** 2027-02-11 (quarterly)

Covers the workflows this team has delegated or partially delegated
to AI assistants per `docs/ROUTING_MATRIX.md` and `AGENTS.md`. Names
the hours we expect each team member to get back, where we have
decided they should go, how we'll measure that they went there, and
what we'll do if they don't.

This is a contract between me (the manager) and the team. It is NOT
a commitment to our stakeholders to deliver more throughput. The
hours do not belong to the sprint backlog by default.
```

### 2.2 Baseline — where the hours come from

| Workflow (routing-matrix row) | Previous time cost (per IC, per week, estimated) | New time cost (estimated post-automation) | Delta |
|---|---|---|---|
| 1 Internal CRUD endpoint | 6h | 3.5h | **2.5h** |
| 6 Single-file refactor | 3h | 1h | **2h** |
| 8 Unit / integration test authoring | 4h | 2h | **2h** |
| 11 Internal docs and runbooks | 2h | 1h | **1h** |
| **Total freed (estimated)** | **15h** | **7.5h** | **~7.5h/week/IC** |

**Note.** These estimates come from the team's own logged time on these ticket classes over the last two sprints, not from vendor marketing numbers. If the team hasn't logged this, do a two-week baseline measurement before publishing the plan. Over-estimating the savings is the single most common first-draft error here.

Not every hour on the left shows up on the right. Some of it:
- Shifts to **the H→AI direction** — prompt review, eval analysis, ADR critique. That is still work; it's just a different kind of work. The plan treats it as **freed-but-reallocated**, not **freed-for-growth**.
- Dissolves into **context-switching overhead** — jumping between agent sessions. Account for this; it is not free.

Realistic per-IC "freed-for-growth" budget after honest accounting on this team: **~4h/week/IC**, not the headline 7.5h.

### 2.3 Named destinations

For each IC, the plan allocates the ~4h/week freed-for-growth budget across a small set of named destinations. The specific split is per-IC and reviewed in the next 1:1; the *list* of allowed destinations is team-level.

The team's approved destinations for freed attention:

| # | Destination | Why it goes here | Existing signal we'll use to measure |
|---|---|---|---|
| D1 | **ADR authoring and review** on decisions the team has been deferring (Mod-103 Ex-03). | Standards-as-code is the thing that makes AI delegation safer; the more ADRs, the less re-litigation. | ADRs merged per quarter. |
| D2 | **Eval suite design and extension** (Mod-103 Ex-04 / Ex-05). | Every prompt/model change ships through the eval gate; the gate is only as good as the suite. | Eval cases added per quarter; coverage % of production prompts. |
| D3 | **Deliberate learning hours** — pick from the team's curated reading list; one short writeup per month (Mod-107). | Anti-atrophy. The ICs delegating code to AI need to stay sharp on the stuff AI doesn't do well. | Learning hours logged; monthly writeup merged to `docs/learning/`. |
| D4 | **Mentoring / pairing** — a weekly 1h paired session with a junior on the team or across teams. | AI delegation disproportionately risks junior ICs' skill growth. Senior-to-junior pairing is the counter-pressure. | Pairing sessions logged in `docs/mentoring/`; junior feedback at quarterly review. |
| D5 | **Incident drill / tabletop participation** (Mod-109). | Incidents are exactly where AI-adjacent misses land; drill-readiness is the thing you can only build before. | Drills participated in per quarter; postmortem action items closed. |
| D6 | **Routing-matrix / policy maintenance** — the quarterly review of Ex-01 / Ex-02 / Ex-03 / Ex-04 artifacts. | The artifacts rot without upkeep. The hours for upkeep must come from somewhere; this is where. | Quarterly-review commit lands; artifact age < 90 days. |

Per-IC split (worked example for one team of 5 ICs):

| IC | D1 ADR | D2 Eval | D3 Learn | D4 Mentor | D5 Drill | D6 Upkeep | Total / week |
|---|---|---|---|---|---|---|---|
| IC-A (senior) | 1.0 | 0.5 | 0.5 | 1.5 | 0.25 | 0.25 | **4.0h** |
| IC-B (senior) | 0.5 | 1.0 | 0.5 | 1.0 | 0.5 | 0.5 | **4.0h** |
| IC-C (mid) | 0.5 | 0.5 | 1.5 | 1.0 | 0.25 | 0.25 | **4.0h** |
| IC-D (mid) | 0.5 | 1.5 | 1.0 | 0.5 | 0.25 | 0.25 | **4.0h** |
| IC-E (junior) | 0.25 | 0.25 | 2.5 | 0.75 (receives) | 0.25 | 0 | **4.0h** |

The split is deliberately **weighted by career stage**. Juniors should get the smallest share of policy/eval/ADR work and the largest share of learning and received mentoring; seniors should carry upkeep and mentoring. This is the inverse of the common failure mode where senior ICs hoard the interesting work and juniors inherit the busywork.

### 2.4 Named refusals

Hours freed by this plan **will not** go to any of the following, by team-manager commitment:

- **More tickets per sprint.** The sprint size is unchanged this quarter. Any throughput increase is a bonus, not a target.
- **More meetings.** The meeting calendar is frozen this quarter; new recurring meetings require the manager's approval and a removed meeting of equal or greater size.
- **"Expand Slack responsiveness" / "be more available".** Focused hours are the scarce resource. ICs are expected to have Slack-off blocks.
- **Stakeholder-facing "capacity to take on more".** The manager pushes back on external requests that treat the freed hours as fungible capacity. The hours are already allocated.
- **Expanding the agent's scope unilaterally.** Scope expansion goes through the routing-matrix promotion path (Ex-01 §2.4). Freed hours do not translate to "let the agent do more".

Naming the refusals is as important as naming the targets. Without them the plan quietly collapses into the ambient demand gradient of the organisation.

### 2.5 Measurement and cadence

- **Weekly, in 1:1s:** manager asks each IC: "Did the ~4h land in D1-D6 this week? If not, where did it go?" The manager tracks the answer briefly, without productivity-theatre instrumentation.
- **Monthly, in team review:** check the existing signals (ADRs merged, eval cases added, mentoring sessions logged, drills participated in, learning writeups merged). These are *team-level* metrics the manager already has access to, not new tooling.
- **Quarterly:** revise this plan. Was the estimate correct? Did any destination grow or shrink? Does the per-IC split still match career stages? What were the drains that pulled hours elsewhere?

### 2.6 Failure plan

If, at the monthly review, the signals do not show the redirection happening (ADRs flat, learning writeups zero, mentoring not booked), the manager does the following **in order**:

1. **Diagnose**: 1:1 with each IC — is the time appearing at all? If yes, where is it going? If no, is the automation estimate wrong?
2. **Clear drains**: identify the unnamed destinations (extra meetings, escalated support, quiet scope creep) and name them. Decide which to cut.
3. **Reduce the allocation realistically**: if 4h/week was optimistic, revise to a lower number that holds. An honest 2h/week is better than an aspirational 4h/week.
4. **Protect the destinations explicitly**: the manager blocks calendar time for the destinations that keep getting displaced, especially learning and mentoring.
5. **Escalate** to the team manager's own manager if upstream demand is the structural cause. The plan is a contract between team manager and team; if upstream demand is the reason the contract can't be kept, that is a conversation with the next level up, not a quiet surrender.

## 3. Decision rationale

- **Honest baseline, not vendor marketing.** Starting from a measured baseline (two weeks of logged time on the affected workflows) is the only way to avoid planning against inflated savings estimates. Headline "AI saves X%" numbers are almost always context-free.
- **Explicit freed-but-reallocated bucket.** Some hours convert to H→AI reviewing work; those hours are not available for growth. Not acknowledging this is the second-most-common first-draft error.
- **Context-switching cost.** The third-most-common error is ignoring the overhead of running multiple agent sessions in parallel. The plan deducts for this up-front so the budget is realistic.
- **Destinations named at the team level, split per-IC by career stage.** Team-level approval lists keep the destinations stable; per-IC splits let seniors carry upkeep and juniors receive mentoring. The inversion-by-default is worth calling out explicitly.
- **Refusals as first-class.** Naming where the hours will not go is the manager's structural job. Without it, the hours diffuse. This is directly motivated by the broader observation (visible in every team that has run this experience) that productivity gains default to the ambient demand gradient unless someone actively holds the line.
- **Measurement using existing signals.** Introducing a new instrumentation stack is productivity theatre. Count ADRs, eval cases, writeups, mentoring entries. These are already in the team's systems (git, docs folder, calendar).
- **Named failure plan.** The plan is more valuable than a one-page commitment, because a plan describes what the manager does when the commitment doesn't hold. The monthly-check / diagnose / clear-drains / protect-calendar / escalate sequence is explicit.
- **Mapped to anti-atrophy and incident-readiness destinations.** The destinations aren't chosen for aesthetic reasons — they are the categories AI delegation makes *more* important, not less. Mod-103 (standards), Mod-107 (anti-atrophy), Mod-109 (incidents) are where this curriculum covers the underlying rationale; this exercise is the operational hand-off to those modules.
- **Signed by the manager, not the team.** This is important. The team cannot be asked to agree to a plan that depends on the manager holding the line against stakeholder requests. The manager owns that part; the team owns showing up to the destinations.

## 4. Validation steps

1. **Baseline-is-real check.** The per-workflow time estimates in §2.2 come from two weeks of logged time, not vendor copy. If they don't, the first action is to collect that baseline before publishing.
2. **Honest-budget check.** The "freed-for-growth" number after subtracting freed-but-reallocated and context-switching overhead is <= 60% of the headline savings. If it's higher, the deductions are too optimistic.
3. **Destinations exist in the team's artifacts.** D1 (ADRs) implies `docs/adrs/` exists; D2 (evals) implies `tests/evals/` exists; D3 (learning) implies `docs/learning/`; D4 (mentoring) implies `docs/mentoring/`. The plan must point at real paths; if any destination doesn't have an artifact home, create one.
4. **Signals exist in existing systems.** For every destination, name a signal the manager can check in <5 min from existing tools (git log, docs folder, calendar). If the signal requires a new tool, pick a different signal.
5. **Per-IC split test.** Walk the per-IC table aloud. Does each row make sense for that IC's career stage? Does the junior receive more mentoring than the senior? Is the senior carrying upkeep?
6. **Refusals read aloud.** The named refusals (§2.4) should be read aloud with the team manager's own manager present once. If the upstream manager pushes back on any refusal, the plan has to either change or be defended now — not after the first time it bites.
7. **Dry-run the failure plan.** Simulate one month of the signals being flat. Walk through the diagnose → clear-drains → reduce → protect → escalate sequence on paper. If step 5 (escalation) feels impossible politically, the plan is fragile; address that before publishing.
8. **Quarterly reminder.** Calendar event or ticket for the next review date in the header.

## 5. Rubric

Grade a submitted plan 0–3 per row.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Honest baseline** | Vendor numbers | Rough estimate | Two-week measured baseline | Measured + deducted for freed-but-reallocated + context-switching |
| R2 | **Named destinations** | "Use the time wisely" | 1–2 destinations | 3–5 named destinations with signals | 3–6 destinations, each with signal + existing-tool check |
| R3 | **Per-IC split** | None | One rate for everyone | Weighted by career stage | Weighted by career stage + juniors receive mentoring + seniors carry upkeep |
| R4 | **Named refusals** | None | 1 refusal | 3+ refusals including "no more tickets" and "no more meetings" | 3+ refusals + stakeholder-facing refusal + agent-scope-expansion refusal |
| R5 | **Measurement cadence** | None | Yearly | Monthly or quarterly | Weekly 1:1 + monthly team + quarterly revise |
| R6 | **Existing-signal preference** | New tool required | Mix | Existing signals only | Existing signals + manager-checkable in <5 min |
| R7 | **Failure plan** | None | "We'll adjust" | Diagnose / adjust steps | Diagnose / clear-drains / reduce / protect / escalate — in order |
| R8 | **Ownership & contract framing** | No owner | Owner only | Owner + explicit "not a stakeholder commitment" | Owner + "not stakeholder commitment" + manager signs + team acks |
| R9 | **Tie to adjacent modules** | None | Mentions another module | Destinations map to ADR / eval / mentoring / incident / anti-atrophy modules | Mapped and signals land in those modules' artifacts |
| R10 | **Revision cadence** | None | Named | Named + triggers | Named + triggers + what-we-check-at-revision prescribed |

**Pass bar: 22/30.**

## 6. Common mistakes

- **Planning against headline AI-savings numbers** (20%, 50%, "X engineers' worth"). These numbers are not measured on your team; building a plan on them produces a plan that collapses in week 3.
- **Not deducting freed-but-reallocated time.** H→AI review work is not free. If your plan treats it as freed-for-growth, your budget is inflated.
- **Ignoring context-switching overhead.** Running two agent sessions in parallel is not twice the throughput of one; it's less than one if context-switching cost is not planned for.
- **"Let people use the time however they want."** Diffusion is the failure mode. 4h/week with no named destinations ends up as ambient Slack.
- **Pointing at destinations that have no artifact home.** "Mentoring" with no tracked log, "learning" with no writeup requirement — these get skipped first when the week gets busy.
- **No named refusals.** Without them the hours quietly flow to extra tickets, meetings, and stakeholder requests.
- **Inverted-by-default per-IC split.** Senior ICs hoard the interesting growth work (ADRs, learning) and juniors get the routing-matrix upkeep. The plan must consciously invert this.
- **New instrumentation stack.** If measurement requires a tool the team doesn't already have, measurement won't happen. Use existing signals.
- **No failure plan.** The plan is fine until the hours don't appear in week 4. Without a named response sequence, the manager gives up or quietly rewrites the plan into aspiration.
- **Commitment to stakeholders rather than to the team.** The plan becomes a capacity commitment to the business, then collapses the first time a priority shifts. Keep it as a manager-to-team contract.
- **Treating the plan as a one-time document.** The estimate is wrong on first draft; the destinations shift as the team learns. Quarterly revision is non-optional.

## 7. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — Govern 1.2 (characteristics of trustworthy AI are integrated into organizational policies, processes, and procedures) and Measure 2.11 (fairness and bias assessments across the AI lifecycle) motivate the explicit attention to mentoring, learning, and incident drills as the "integration" surface at team scope. Manage 4.1 (monitoring of AI systems is planned and performed) motivates the measurement-and-cadence section.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — supplies the risk framing that justifies redirecting a share of freed time to eval-suite maintenance and incident-drill readiness (the surfaces where ML-security failures land).
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-technique taxonomy; motivates the drill-readiness destination (D5) as a category rather than one-off exercises.

<!-- follow-up: once the plan has been in place for a quarter, replace the §2.2 estimates with the measured actuals and recompute the honest freed-for-growth number. The gap between v0.1 estimate and v0.2 measured is the team's calibration delta; name it in the next revision. -->
