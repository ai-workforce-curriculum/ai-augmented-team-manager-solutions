# Exercise 01 — The Day 0 / 30 / 60 / 90 Enablement Plan

> Module: `mod-105-team-ai-enablement-programs`
> Exercise type: **design** (worked plan + decision rationale + grading rubric)

## 1. Solution overview

A team manager inherits or stands up an AI enablement program and almost always makes the same two mistakes: (a) trying to roll everything out at once — tool licences, policies, training, evals, rituals — and (b) measuring nothing until a quarter has passed and nobody can say what changed. A phased **Day 0 / 30 / 60 / 90** plan is the antidote: it fixes the cadence, gates each stage on a visible artifact, and forces the manager to answer "what are we actually proving in this phase?" before spending the team's attention.

The deliverable is a one-page plan the manager can post in the team's wiki on Day 0. It has four phases, each with:

- **A theme** (what this phase is *for* — not a laundry list of tasks).
- **Entry criteria** (what must be true before the phase starts).
- **Deliverables** (artifacts the team can point to — not abstract outcomes).
- **Exit criteria** (what must be true before advancing).
- **A named owner per deliverable** (manager, tech lead, each IC).

Design decisions this solution encodes:

1. **Phases are themed, not tool-scoped.** "Day 30 is when we turn on Copilot" is a weak phase — it describes a procurement event, not a learning milestone. "Day 30 is when every IC can demonstrate one AI-assisted PR that passed review" is a phase.
2. **Day 0 is "foundations and consent," not "launch day."** The team manager does not ship tooling until the accountability contract, the acceptable-use policy, and the baseline survey exist. Rolling tools out before these exist invites the "who's responsible?" vacuum that NIST AI RMF **Govern 1.1** is designed to prevent.
3. **Day 30 is "fluency floor."** Every IC produces one artifact that proves they can use the primary tool safely. Nobody is left behind, and nobody gets to opt out without an explicit manager-signed exception.
4. **Day 60 is "workflow integration."** The focus shifts from *individual* use to *workflow* use — reviews, rituals, tickets, and on-call all accommodate AI-assisted output. See Exercise 03 (Fluency-Growth Measurement) for the measurement artefacts that confirm this.
5. **Day 90 is "proof and handoff-readiness."** The manager compiles evidence that the program worked, decides what to keep, and prepares the handoff contract (see Exercise 05) in case the program scales up to the org. This is a *decision point*, not a celebration.
6. **Every phase has a "what if we should stop?" gate.** If baseline numbers show no measurable fluency gain by Day 60, the program is paused and reviewed — the plan contains its own rollback criteria. This aligns with NIST AI RMF **Manage 2.3** (mechanisms to supersede, disengage, or deactivate deployed systems that demonstrate performance below expectations).

## 2. Worked implementation — the plan

### 2.1 One-pager (the artifact you post on Day 0)

```markdown
# AI Enablement Plan — Team <X>, Quarter <Q>

Owner: <manager name>
Scope: <N> ICs on <team>, primary tools in trial: <tool A>, <tool B>
Review cadence: weekly 15-min stand-up item + monthly phase review

## Phase 0 — Day 0 (foundations and consent)
Theme: nothing ships to the team until the accountability + policy layer exists.

Deliverables (owner):
- Accountability contract, signed by manager and team lead  (manager)
- Acceptable-use policy — one page, derived from the org's AI policy  (manager)
- Baseline survey: current AI familiarity, current workflows, concerns  (manager)
- Tool-budget distribution rubric — see Exercise 02  (manager)
- Baseline "golden PRs" — 3 recent non-AI PRs we'll re-run through assisted workflows later to compare  (tech lead)

Entry criteria: org-level AI policy exists and permits this scope.
Exit criteria: all five deliverables are in the team wiki; every IC has
  read and acknowledged the policy.

## Phase 1 — Day 1 to Day 30 (fluency floor)
Theme: every IC can demonstrate one AI-assisted PR that passed review
  using our team's review checklist (see mod-103 ex-01).

Deliverables (owner):
- Each IC: one merged AI-assisted PR, with the checklist attached,
  tagged `ai-assisted` in the PR description  (each IC)
- Each IC: a 2-paragraph write-up of "what the assistant got wrong"
  from that PR  (each IC)
- Weekly 15-minute "assist demo" slot in team sync, rotating owner
  (manager; rotation set Day 0)
- Mid-phase pulse survey: have blockers surfaced?  (manager, Day 15)

Entry criteria: Phase 0 exit criteria met; tool licences provisioned.
Exit criteria: every IC has submitted their PR + write-up; no
  unresolved safety or policy exceptions.
Rollback trigger: ≥30% of ICs blocked on tooling or policy after Day
  20 → pause rollout, resolve blockers, slip Phase 2 by two weeks.

## Phase 2 — Day 31 to Day 60 (workflow integration)
Theme: our rituals and pipelines accommodate AI-assisted output
  without special-casing it.

Deliverables (owner):
- AI review checklist is wired into the PR template on main repo
  (tech lead)
- Incident-response runbook updated: how to triage an AI-assist
  related bug (on-call lead)
- One "workflow redesign" experiment — pick one workflow (code review,
  on-call triage, docs drafting) and run two weeks of before/after
  measurement on it (manager + one IC)
- Tool-budget mid-quarter review: who's using what, where spend is
  concentrated, which licences to reclaim  (manager)
- Day-60 pulse survey: self-assessed fluency change vs. baseline
  (manager)

Entry criteria: Phase 1 exit criteria met.
Exit criteria: workflow experiment produced a decision (adopt / drop /
  continue experiment); pulse survey shows a positive shift on at
  least two fluency items.
Rollback trigger: pulse survey shows no positive shift AND workflow
  experiment produced null or negative result → convene a review
  with team; decide whether to continue, pause, or redirect scope.

## Phase 3 — Day 61 to Day 90 (proof and handoff-readiness)
Theme: produce a one-page internal report the org can use to decide
  whether to scale our practices up.

Deliverables (owner):
- Fluency-growth measurement artifact set (Exercise 03) complete
  (manager)
- Revised accountability contract (patches from 90 days of use)
  (manager)
- One-page "what we kept, what we killed, what we still don't know"
  memo (manager)
- Handoff contract draft to org-wide enablement lead, if one exists
  (Exercise 05)  (manager)
- Retrospective with team: what we would do differently on Day 0 for
  the next team (manager)

Entry criteria: Phase 2 exit criteria met.
Exit criteria: memo is shared with the manager's manager and any
  relevant program lead; handoff contract is in draft; retrospective
  notes are in the wiki.
```

### 2.2 Decision rationale (why these choices and not others)

- **Four phases, not three or six.** Three phases compress too much into Phase 1 ("learn and integrate at once"); six over-schedule a team that already has a day job. The 30-day cadence lines up with most monthly review rituals and with the cadence of most engineering feedback instruments.
- **Phase 0 is non-skippable.** Teams that skip it find themselves retrofitting policy during Phase 2, usually in response to an incident. The accountability contract and baseline survey are cheap to produce on Day 0 and expensive to produce under pressure on Day 45. This mirrors the **Govern** function in the NIST AI RMF — governance is set up *first*, not alongside the first deployment.
- **One-PR-per-IC as the Day 30 bar.** This is deliberately low. It is not a productivity metric; it is a *participation* metric. The purpose is to prove that nobody is quietly opting out, and that every IC has *one* concrete artifact we can review together. Raising this bar (say, three PRs each) swaps participation for throughput and converts the phase into a volume contest.
- **"What the assistant got wrong" write-ups.** This flips the usual framing. Every IC must document a failure mode they personally encountered. That corpus becomes the input for the Exercise 03 fluency measurement, the input for updating the review checklist, and the single most valuable artifact of the quarter.
- **Workflow experiment in Phase 2.** The riskiest version of enablement is "everyone uses the tool for whatever they want." The safest and most measurable version is "we pick one workflow, instrument it, measure before and after." One bounded experiment per team per quarter produces a defensible before/after comparison and feeds directly into the Day 90 memo.
- **Explicit rollback triggers.** Without stated conditions for pausing, the plan becomes a calendar. The rollback triggers are the governance control that lets the team manager stop momentum when the evidence demands it, in line with NIST AI RMF **Manage 2.3**.
- **Phase 3 ends with a memo, not a celebration.** Celebrations make it impossible to say "that didn't work." A memo with a "what we killed" section *forces* the manager to admit which pilots failed, and that honesty is the thing that makes the next quarter's plan useful.

## 3. Validation steps

1. **Walk the plan backwards from Day 90.** Pick the single most important decision you expect to make on Day 90 (e.g., "whether to extend tool licences to the full team"). Trace which Phase 2 deliverable informs it, which Phase 1 artifact that deliverable depends on, and which Phase 0 foundation that artifact rests on. If the chain breaks, the plan is missing a deliverable.
2. **Dry-run the rollback trigger.** Imagine the Phase 1 pulse survey shows 40% of ICs blocked on tooling at Day 20. Run through the plan: what *exactly* happens? Who calls it? Where do the follow-up tasks live? If the answer is vague, tighten the trigger.
3. **Peer-review with one other manager.** A manager who ran a similar rollout last quarter can red-line in ten minutes. Their marginal edits (the "we tried that and it didn't work because …" items) are the highest-leverage changes you will get.
4. **Check deliverable ownership.** Every deliverable must have exactly one named owner. If any deliverable's owner is "the team" or "everyone," reassign it. Diffuse ownership is the single most common cause of phase slippage.
5. **Scope-check against calendar reality.** Pull the team's known commitments (release cycles, on-call rotations, out-of-office). If any phase overlaps a release freeze, slip the phase or carve a reduced deliverable set. A plan that ignores calendar reality is a plan that will collide with it.
6. **Share the one-pager with the team on Day 0 and ask for one edit each.** Any IC who cannot find one edit they want to propose has not read it carefully. Give them twenty-four hours, incorporate what's reasonable, then freeze.

Validation is *behavioural*, not stylistic — you are checking that the plan survives contact with your team's actual calendar, and that each phase has a decision it is designed to inform.

## 4. Rubric

Use this to grade a submitted plan (self-assessment or peer review). Each criterion scores 0–3.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Phase themes** | Phases named by date only, or by tool rollout events | Themes mention learning but are vague | Themes are concrete learning milestones | Themes are concrete milestones that each inform a specific decision |
| R2 | **Entry / exit criteria** | None | Only some phases have them | Every phase has entry + exit criteria | Entry + exit are testable (checkable artifacts, not vibes) |
| R3 | **Named owners** | "The team" | Some deliverables have named owners | Every deliverable has exactly one named owner | Owners are named and have visible capacity on their calendar |
| R4 | **Baseline + measurement wiring** | None | Baseline survey only | Baseline + mid-phase pulse | Baseline + Day 30 + Day 60 + Day 90 instruments, each feeding the Exercise 03 artifact set |
| R5 | **Rollback triggers** | None | One generic "we might pause" | Explicit trigger per phase | Explicit trigger + named decision-maker + follow-up procedure per phase |
| R6 | **Policy / accountability upfront** | Not mentioned | Mentioned in Phase 2 or later | In Phase 0, one artifact | Accountability contract + acceptable-use policy + tool-budget rubric all in Phase 0 |
| R7 | **Workflow-level experiment** | None | "We will try the tool" | One bounded experiment with before/after | One bounded experiment with pre-registered metrics, control period, and decision criteria |
| R8 | **Day 90 deliverable is decision-ready** | "Celebration" or no artifact | Retrospective notes only | One-page memo | One-page memo + handoff contract + updated accountability contract + "what we killed" section |
| R9 | **Fits the team's calendar reality** | Ignores release and on-call rotations | Mentions but doesn't adjust | Adjusts for known freezes | Co-planned with release/on-call owners, documented in the plan |

**Pass bar: 18/27.** Below that, iterate before posting the plan to the team.

## 5. Common mistakes

- **Treating Day 0 as "launch day" instead of foundations.** Rolling out tool licences before the accountability contract exists leads to the "who's responsible?" vacuum that produces the first avoidable incident of the quarter.
- **Setting the Day 30 bar on volume, not participation.** "Three merged PRs each" sounds rigorous but creates incentives to pad. One merged PR + a written failure-mode reflection is a better fluency signal than three merged PRs without reflection.
- **Making every deliverable the manager's.** The manager becomes the bottleneck and the team never develops local ownership. Owners should rotate across ICs and the tech lead for everything except governance artifacts.
- **No pulse survey between phases.** Without Day 15 and Day 45 pulses, the manager learns about blockers at the phase boundary — when it is too late to correct within the phase.
- **Scheduling Phase 3 over a holiday period.** Day 61–90 overlapping with December is a classic failure — the Day 90 memo lands when nobody is around to read it. Shift the plan or compress the phase.
- **Reusing another team's plan verbatim.** Enablement plans are team-shaped — different stack, different review culture, different risk tier, different calendar. A copied plan fails at R9.
- **Omitting rollback triggers "because they feel defeatist."** The opposite is true: a plan with rollback triggers is credible precisely because it admits it might not work. Without them, the plan is a schedule, not a program.
- **Confusing "scaled" with "worked."** The Day 90 memo should ask whether the *team's practice* improved, not whether the *tool rollout* completed. Completion is not success.
- **Not writing the handoff contract (Exercise 05) in Phase 3.** If the program will persist, the contract is cheaper to draft while the context is fresh than to reconstruct a quarter later.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the **Govern** function (accountability, policy, roles) is the backbone of the Phase 0 deliverable set; the **Manage** function (mechanisms to supersede, disengage, deactivate) is the source of the rollback trigger pattern.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — supplies the risk framing that justifies the Phase 0 acceptable-use policy and the Phase 2 incident-runbook update. Specific security review items for AI-assisted artifacts live in the mod-103 review checklist (Exercise 01) and mapping to OWASP LLM Top 10 (Exercise 06).
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat taxonomy used to sanity-check that the Phase 2 incident runbook covers the techniques most likely to apply to an AI-assisted coding and documentation workflow.

<!-- follow-up: once the team has completed one Day 90 cycle, replace the baseline "one merged PR per IC" bar with the empirical participation rate observed, so the next team using this plan has a calibrated target. -->
