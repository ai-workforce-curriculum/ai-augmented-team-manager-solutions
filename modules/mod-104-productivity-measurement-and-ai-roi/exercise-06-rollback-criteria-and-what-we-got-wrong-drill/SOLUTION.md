# Exercise 06 — Rollback Criteria and the "What We Got Wrong" Drill

> Module: `mod-104-productivity-measurement-and-ai-roi`
> Exercise type: **design** (worked criteria + drill walk-through + grading rubric)

## 1. Solution overview

The deliverable is a **rollback-criteria doc plus a "what we got wrong" drill** that a team manager can commit to the team repo and rehearse once a quarter. Together they answer two questions you must be able to answer before expanding any AI rollout:

- "**What would make us roll this back?**" — written down in advance, with thresholds and owners, so the decision is not re-litigated during a crisis.
- "**What did we believe at the last pre-read that turned out to be wrong?**" — rehearsed on a schedule, so the team gets better at reading its own instruments and does not quietly accumulate overconfident claims.

A good rollback-criteria doc is:

- **Pre-committed.** Thresholds are set in calm weather. Deciding mid-incident what "unacceptable" means is deciding on a bad day.
- **Threshold-and-trigger-shaped.** "If X exceeds Y for Z weeks, we do W." Prose-only criteria do not survive contact with a Friday afternoon.
- **Reversible.** A rollback is not a cancellation. It is a *pause-and-learn*. The doc specifies the re-entry criteria too.
- **Scoped to team authority.** The team manager cannot cancel a company-wide tool contract; they can roll back scope on their own team. The doc respects that boundary.

A good "what we got wrong" drill is:

- **Blameless.** The drill exists to make the team better at estimating under uncertainty, not to litigate past decisions.
- **Specific.** Each entry names what we said, when we said it, and what turned out to be different, with a pointer to the pre-read it came from.
- **A habit, not an event.** Quarterly, tied to the pre-read cycle. Ten minutes, not an incident review.

Design decisions this solution encodes:

1. **Rollback criteria are owned by the team manager, with a named escalation.** The team manager decides pause-and-learn within team scope; escalations above team scope go to the named escalation path, pre-committed in the doc.
2. **Criteria are categorical, not vibes.** Change failure rate, incident severity, policy violation, privacy incident. Each has a numeric or named trigger.
3. **Rollback is not a single event.** The doc distinguishes scope reduction, tool cohort reduction, feature-level rollback, and full suspension. Each has its own trigger band.
4. **The drill reads previous pre-reads and interviews.** It is the compounding mechanism for institutional memory. See Exercise 04 and Exercise 05.
5. **The drill is not an incident post-mortem.** Different artefact, different cadence, different purpose. Keep them separate.

## 2. Implementation — the rollback criteria and the drill

### 2.1 The rollback-criteria doc

Drop into `governance/rollback-criteria.md`. Reviewed yearly; updated whenever an incident, a pre-read, or a drill surfaces a new trigger.

```markdown
# Rollback Criteria — AI Tool Rollout, <team>

> Owner: <team manager> · Escalation path: <director, then CISO for
> security-category triggers> · Last reviewed: <yyyy-mm-dd>

## Principles
- **Pre-commit the thresholds.** We decide now, in calm weather, what
  would make us roll back. We do not re-litigate mid-crisis.
- **Rollback is a pause-and-learn, not a cancellation.** Every criterion
  below pairs with a re-entry criterion.
- **Thresholds are numeric or categorical, never vibes.**
- **The team manager owns in-scope decisions.** Out-of-scope decisions
  route via the escalation path.

## Scope of control
The team manager can, on their own authority:
- Reduce the cohort using the tool (e.g., pause rollout to the newest
  engineers while incumbents keep access).
- Disable a specific feature (e.g., agent-mode, auto-apply, auto-commit).
- Restrict the tool to specific repos or workflows.

The team manager cannot, on their own authority:
- Cancel a company-wide vendor contract.
- Change a company-wide policy on AI tool use.

Escalations above team scope go to <director> within one business day
of trigger.

## Trigger table

Each row names the metric or category, the threshold, the sustained
window, the response action, and the re-entry criterion.

### Category A — Quality / reliability triggers

| # | Metric | Threshold | Window | Action | Re-entry criterion |
|---|---|---|---|---|---|
| A1 | Change failure rate | > 1.5× pre-rollout baseline (or absolute > 15%, whichever higher) | 4 consecutive weeks | Disable agent-mode auto-apply team-wide; revert to review-only. | 4 consecutive weeks of CFR within ±10% of pre-rollout baseline. |
| A2 | Rollback count (per week) | > 2× pre-rollout baseline | 2 consecutive weeks | Reduce cohort to incumbents; onboard newest engineers under code-review requirement before AI-assist use. | 2 consecutive weeks within baseline band. |
| A3 | Sev-1/Sev-2 incident clearly caused by AI-generated code | 1 incident, any single week | Any | Immediate scope reduction: disable the feature implicated; team-manager review within 1 business day; escalate. | Explicit written sign-off from the team manager + director that the root cause has been mitigated. Named date for re-enabling, not "until we feel better." |

### Category B — Security / privacy triggers

| # | Metric | Threshold | Window | Action | Re-entry criterion |
|---|---|---|---|---|---|
| B1 | Any secret, credential, or customer PII leaked into AI assistant context (regardless of whether it was logged / stored) | 1 event | Any | Immediate full suspension of the affected integration. Follow incident-response (see Module 109). Preserve logs. | Vendor- and internally-confirmed remediation + control added (allowlist, DLP, scanner) + incident retrospective completed. |
| B2 | Prompt injection resulting in unauthorised action by an agent | 1 event, any severity | Any | Immediate feature-level rollback of the affected agent integration. Full rollback review. | Allowlist / parser / human-in-loop control added + internal red-team pass. |
| B3 | Vendor security advisory or data-handling incident affecting the tool | 1 event | Any | Pause rollout to new users. Review scope within 1 business day. Full rollback if the incident touches our data. | Vendor-published remediation + internal review + signed-off re-enable. |

### Category C — Cost / budget triggers

| # | Metric | Threshold | Window | Action | Re-entry criterion |
|---|---|---|---|---|---|
| C1 | Variable usage overrun vs. forecast | > 110% of forecast | 2 consecutive weeks | Escalate to budget rebase (not mid-sprint throttle). Update forecast. If the cause is pathological (one actor, one workflow), scope-reduce the offender. | Rebase approved or the specific workflow throttled. |
| C2 | Second-order spend (CI, cloud compute) | > 2× the reserve line | 1 week | Scope-reduce the agentic workflow driving it. Investigate root cause before re-enabling. | Root cause named and mitigated; reserve-line math re-checked. |

### Category D — People / DevEx triggers

| # | Metric | Threshold | Window | Action | Re-entry criterion |
|---|---|---|---|---|---|
| D1 | Quarterly DevEx score (Q7 "recommend this team") | Drop > 0.5 points vs. prior quarter, from a baseline ≥ 3.5 | 1 quarter | Open a "what changed?" interview round (Exercise 05); defer any rollout expansion the following quarter. | DevEx returns to within 0.2 of baseline. |
| D2 | Weekly pulse Q3 ("AI net-helpful") shows > 25% "harmful" | 2 consecutive weeks | 2 weeks | Open interview round focused on the harm pattern; re-scope or disable the specific feature named. | Share of "harmful" returns < 10% for 2 consecutive weeks. |
| D3 | Qualitative theme "verification overhead" appears in ≥ 3 interviews | 1 quarter | Any | Re-scope tool use away from the workflow where the overhead is highest. Add deterministic-check or human-in-loop guardrail. | Theme does not recur in next quarter's interviews. |

### Category E — Policy / compliance triggers

| # | Metric | Threshold | Window | Action | Re-entry criterion |
|---|---|---|---|---|---|
| E1 | Any use of the tool outside the written policy scope (e.g., on customer PII, auth code, prohibited repos) | 1 event, discovered | Any | Mandatory refresher; team-manager-led review; scope-adjust if the policy is ambiguous. | Policy clarified; refresher completed by all users. |
| E2 | External or regulatory audit finding about the tool | 1 finding | Any | Suspend the specific use the finding targets. Escalate. | Audit closure. |

## The decision flow
- Any trigger in Category B (security/privacy) → **immediate action**.
  Decide first, write it up second.
- Any trigger in Categories A, C, D, E → **document, decide within
  1 business day, communicate within 2.** No mid-sprint throttling on
  cost triggers (see Exercise 03 policy).

## Who gets informed
- On any Category A or B trigger: the escalation path, within 1 business
  day.
- On any trigger that reaches the "Action" column: the team itself,
  within 2 business days, with a short one-paragraph note in the team
  channel.

## What rollback does NOT do
- It does not blame individuals.
- It does not void the rollout. Re-entry criteria are explicit.
- It does not require a full post-mortem for every event (Category B
  events, yes; others, no — a one-paragraph note in the quarterly
  pre-read is sufficient).
```

### 2.2 The "what we got wrong" drill

A quarterly 30-minute drill. Runs in the same week as the ROI pre-read (Exercise 04) is drafted, before the pre-read is finalised. Attendees: the team manager and one senior IC. Output: a one-paragraph entry in `governance/wwgw-log.md`.

```markdown
# "What We Got Wrong" Drill — <team>, <quarter>

> 30 minutes. Blameless. Attendees: team manager + 1 senior IC.
> Output: one paragraph into wwgw-log.md. Feeds the next pre-read.

## 1. Pre-work (5 min before the meeting)
- Open the previous quarter's pre-read (Exercise 04 archive).
- Highlight every claim with a numeric target or a confounder mention.

## 2. The drill (30 min)

### Round 1 — The claims (10 min)
For each highlighted claim, answer in writing:
- What did we say? (One sentence.)
- What actually happened? (One sentence.)
- Is the difference meaningful? (yes / no / not yet known)

### Round 2 — The confounders (10 min)
For each confounder we named in the previous pre-read:
- Did it materialise? (yes / no / unclear)
- Did a confounder we did *not* name turn out to matter? If so, name it.

### Round 3 — The reversal criteria (5 min)
- Did any Category A/D trigger fire that we did not notice? Why not?
- Did any trigger we expected to fire *not* fire? Did we have the wrong
  threshold, or is the signal real but below the band?

### Round 4 — The pattern (5 min)
- Across the last 2–3 quarters, what is our most systematic pattern of
  over- or under-claiming? (We over-attribute speedups to the tool; we
  under-weight CFR movement; we assume survey response shifts are small
  when they are not; etc.)
- What is the single habit change for next quarter that would most
  reduce this pattern?

## 3. Output
Write one paragraph (≤ 150 words) into `wwgw-log.md`:

> **<quarter>.** Headline over-claim: <one sentence>. Headline
> under-claim: <one sentence>. Confounder we missed: <one sentence>.
> Habit change for next quarter: <one sentence>. Log entry author:
> <name>.

This paragraph is pasted verbatim into the top of the next quarter's
ROI pre-read (Exercise 04), under a section titled "What we got wrong
last quarter."

## Ground rules
- No named individuals outside the two attendees.
- No conclusions about performance of any engineer.
- No conclusions about the quality of the tool in isolation; only
  about *our interpretation* of it.
- The drill is not a post-mortem. Post-mortems address incidents;
  this drill addresses claims.
```

### 2.3 Why the rollback doc is shaped this way

- **Pre-committed thresholds** are the single highest-leverage design decision. When CFR trends up during a stressful sprint, the team re-litigating "is this bad enough?" is the pattern that produces harm. Thresholds set in advance remove that re-litigation.
- **Categories map to who can decide.** Quality/reliability triggers (Category A) are team-manager-decidable. Security/privacy (Category B) are *not* — they route to incident response and the security chain. Separating categories makes the escalation path unambiguous.
- **Re-entry criteria are mandatory.** A rollback with no re-entry criterion becomes a de facto cancellation — scope quietly withers. Writing the re-entry condition at the same time as the trigger closes the loop.
- **"Immediate action" is reserved for Category B.** Over-using "immediate" creates false urgency on triggers that benefit from a day of thought.
- **No mid-sprint throttling on cost.** Cost triggers escalate to budget rebase; they do not interrupt committed work. See Exercise 03.

### 2.4 Why the drill is shaped this way

- **Short (30 minutes) and small (2 people).** Longer or bigger drills either get skipped or become theatre. The point is habit, not ceremony.
- **Blameless by construction.** The output paragraph names claims and confounders, never individuals.
- **Paragraph pasted into the next pre-read.** This is the compounding mechanism: the next exec audience sees the team's self-correction in writing, every quarter. Over a year, this builds credibility faster than any single headline number.
- **Separate from incident post-mortems.** Post-mortems are about events in the system; the drill is about claims in the pre-read. Different cadence, different output, different attendees.
- **Pattern across quarters.** Round 4 prevents the drill from becoming a quarter-by-quarter list of one-off mistakes with no learning. The systematic pattern is where the leverage is.

## 3. Validation steps

1. **Threshold-realism check.** For each row in the trigger table, pull two prior quarters of actual data (CFR, cost, DevEx, etc.) and check whether the proposed threshold would have fired any triggers. If the answer is "no triggers in any quarter," your thresholds are too loose. If the answer is "triggers every quarter," they are too tight.
2. **Tabletop one Category B scenario.** Walk through the response flow for a hypothetical B1 (PII leaked into assistant context): who is called, what is suspended, how is it logged, who signs re-entry. If more than two of the roles are ambiguous, the doc is not ready.
3. **Tabletop one Category A scenario.** Walk through a slow CFR drift: how it is noticed, who decides, when the team is told, what the one-paragraph note says. Verify the "no mid-sprint throttling" principle survives contact with a specific scenario.
4. **Run the drill retroactively.** Take the previous quarter's pre-read (or, in month one, any available internal write-up) and run the drill as if that quarter had just ended. If the output paragraph surprises you, the drill is doing its job.
5. **Dry-run with a peer manager.** Invite a peer team manager to the first live drill as an observer. Their outside eye catches the premature conclusions you would otherwise sign off on.
6. **Record action-cost.** Measure how long the drill takes (target: ≤30 minutes) and how long it takes to produce the output paragraph (target: ≤10 minutes). Overruns are a signal to narrow scope, not to expand the format.
7. **One-year retrospective.** After four drills, review the `wwgw-log.md`. Look for a recurring pattern (e.g., "we over-attribute speedups three quarters running"). Patterns that survive four quarters are team-shaped and require a structural change to the pre-read or the measurement layer, not just the drill.

Validation is **rehearsal**: you are checking that the doc survives contact with a real scenario and that the drill produces an output paragraph that the next pre-read is willing to open with.

## 4. Rubric

Use this to grade a submitted rollback-criteria doc and drill. Each criterion scores 0–3.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Threshold specificity** | Prose only, no numbers | Some numeric thresholds, inconsistent | Every row names metric + threshold + window | All of 2 + re-entry criterion per row |
| R2 | **Category coverage** | One category | Quality/reliability only | Quality/reliability + security + cost | All four categories (A/B/C/D/E) + policy/compliance leg |
| R3 | **Escalation path clarity** | Not specified | Named but vague | Owner + escalation path + timing | Owner + escalation path + timing + explicit team-scope-vs-out-of-scope distinction |
| R4 | **Category B (security) handling** | Treated like other triggers | Separate but no immediate-action rule | Immediate action + log preservation + incident link | All of 2 + explicit re-entry criterion requiring vendor + internal sign-off |
| R5 | **No mid-sprint throttling on cost** | Hard cut-off rule | Soft cap only | Explicit "no mid-sprint throttle" + escalation-to-budget-rebase | All of 2 + specific pathological-case exception (single actor / workflow) |
| R6 | **Drill design** | No drill | Drill exists, no structure | 4-round structure + 30-min target + 2-person attendees | All of 2 + paragraph-output format + feed-forward into next pre-read |
| R7 | **Blamelessness and ground rules** | Not addressed | Mentioned | Written ground rules | Written ground rules + explicit no-individual-naming rule + separation from incident post-mortems |
| R8 | **Compounding mechanism** | Drill is a one-off | Drill runs but output is private | Drill output logged in `wwgw-log.md` | Log entry pasted into next pre-read + yearly pattern review documented |

**Pass bar: 16/24.** Below that, iterate before publishing to the team.

## 5. Common mistakes

- **Prose-only criteria.** "If things get bad, we'll roll back" is not a rollback criterion. Numeric thresholds and sustained windows are the whole point.
- **No re-entry criteria.** Rollbacks without re-entry quietly become cancellations. Write both at the same time.
- **Mixing security and non-security triggers in the same response flow.** Security triggers are immediate and escalate up; cost triggers rebase and escalate laterally. Collapsing the categories collapses the escalation.
- **Mid-sprint hard throttles on cost.** This is both operationally disruptive and politically expensive. Escalate to the next budget rebase, do not interrupt committed work.
- **Blaming individuals in the drill.** The moment the drill becomes "what did <person> get wrong?" it stops being useful. The first ground rule exists because this is a load-bearing failure mode.
- **Running the drill once and forgetting.** The compounding value is in the quarterly cadence. A one-off drill is theatre.
- **Conflating the drill with an incident post-mortem.** They have different purposes, different attendees, and different outputs. Keep them separate.
- **Letting re-entry decisions be informal.** "We'll turn it back on when things feel better" is not a decision; it is a drift. Named date or named signal.
- **Writing rollback criteria without pulling two quarters of actual data.** Thresholds set without historical data are either toothless or paranoid. Base them on your own baseline + a reasonable headroom.
- **No team communication.** A rollback that happens silently teaches the team the opposite lesson: that quality signals are ignored. Share the one-paragraph note in the team channel within 2 business days.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the **Govern** function (accountability, documented decision-making) and the **Manage** function (prioritise, respond to, and recover from AI risks) are the direct basis for the trigger / escalation / re-entry structure.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) and [OWASP Top 10 for LLM Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — supply the specific risk categories for the Category B (security) triggers: prompt injection (LLM01), insecure output handling (LLM02), sensitive information disclosure (LLM06), excessive agency (LLM08), etc.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial technique taxonomy; used to sanity-check that the Category B triggers cover the threat techniques most likely to apply to coding-assistant use.
- Forsgren, N., Humble, J., & Kim, G. *Accelerate* and **DORA State of DevOps** reports (`dora.dev`) — the Change Failure Rate as a stability signal and the pairing of throughput and stability metrics is the heritage of Category A's thresholds.
- Forsgren, N., Storey, M.-A., Maddila, C., Zimmermann, T., Houck, B., & Noda, J. (2021). *The SPACE of Developer Productivity.* ACM Queue, 19(1). <https://queue.acm.org/detail.cfm?id=3454124> — the Satisfaction & well-being dimension underlies the Category D (people / DevEx) triggers.
- Exercise 03 (cost tracking), Exercise 04 (exec pre-read), Exercise 05 (qualitative instruments), and Module 109 (incident response) — the rollback doc and the drill compose these.

<!-- follow-up: once four drills are in the log, review `wwgw-log.md` for recurring patterns and promote any pattern seen in 3+ consecutive quarters to a structural change in Exercise 04's pre-read template (e.g., a new default row in section 5's confounder table). -->
