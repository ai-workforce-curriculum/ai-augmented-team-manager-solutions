# Exercise 01 — Ritual Calendar Audit and Refresh

> Module: `mod-112-cross-functional-leadership-and-ritual-calendar`
> Exercise type: **design** (worked audit, retirement decisions, grading rubric)

## 1. Solution overview

The deliverable is **v0.1 of the team's audited ritual calendar**: every
recurring ritual on the team's calendar classified against [Lencioni's four
meeting types](https://www.tablegroup.com/product/death-by-meeting/),
anchored to a specific `mod-104` dashboard tile (or a named `mod-109` /
`mod-110` tile), costed in person-hr/yr, and either kept with a defended
tile or retired / right-sized / split with a decision-log entry. The audit
is the time-layer of the team operating system (Chapter 7); it turns the
calendar from an accretion of rituals that "someone added" into a budgeted
set of rituals that each claim responsibility for a specific measurable
outcome.

The solution encodes four design choices that the exercise will be graded
against:

1. **The audit must retire or right-size at least one ritual.** The failure
   mode of a "we revalidate everything" audit is explicitly prevented. The
   discipline is: if the audit produces zero changes, it has been run as
   ceremony rather than inspection. Re-run it skeptically.
2. **Every surviving ritual cites a specific defended tile.** Not "it is
   useful"; the specific tile from the `mod-104` dashboard (or named gap /
   incident tile) whose value gets worse if the ritual stops. Rituals
   without a defended tile are allowed *in the open*, with a one-sentence
   judgement-call justification recorded against them.
3. **Rituals are classified against Lencioni's four types with no
   overloading.** A ritual that is "weekly tactical pretending to be
   monthly strategic" is a split candidate, not a longer tactical slot.
4. **Cross-functional rituals are co-signed, not unilaterally retired.**
   The manager cannot retire a joint ritual with Product, Design, or the
   AI-Governance peer alone; those decisions route through the Chapter 3
   grid and are co-signed by the partner.

## 2. Implementation — worked audit for "Underwriting-assist team" (fictional calibration)

A nine-person engineering team (manager + eight ICs) running an
ML-assisted underwriting workflow. The calendar has accreted over a year
of operation; the manager runs the annual audit. The audit template per
ritual is the Chapter 2 nine-field form: ritual name, cadence, Lencioni
type, attendees, purpose, defended tile, signal-of-decay, owner, last
audit date.

### 2.1 Inventory and costing (step 1–2)

Twelve recurring rituals on the calendar at the time of the audit, plus
cross-functional ritual attendance that is costed but not audited.

| # | Ritual | Cadence | Attendees | Slot | Annual sessions | **Annual cost (person-hr)** |
|---|---|---|---|---|---|---|
| 1 | Standup | Daily (M–Th) | 9 | 10 min | 192 | **288** |
| 2 | Sprint planning | Biweekly | 9 | 60 min | 26 | **234** |
| 3 | Sprint review / demo | Biweekly | 9 | 45 min | 26 | **176** |
| 4 | Retrospective | Biweekly | 9 | 60 min | 26 | **234** |
| 5 | 1:1s (8 directs) | Weekly | 2 | 30 min | 384 | **192** |
| 6 | Dashboard review | Weekly | 5 | 30 min | 48 | **120** |
| 7 | Office hours | Weekly | 3 avg | 60 min | 48 | **144** |
| 8 | AI tools sync | Weekly | 9 | 60 min | 48 | **432** |
| 9 | Eval-set review | Monthly | 4 | 60 min | 12 | **48** |
| 10 | Tool-registry review | Quarterly | 5 | 120 min | 4 | **40** |
| 11 | Governance retro | Semiannual | 9 | 90 min | 2 | **27** |
| 12 | Incident retro | Per-incident (~6/yr) | 6 avg | 60 min | 6 | **36** |
| | **Owned subtotal** | | | | | **~1,971 person-hr/yr** |
| X1 | Product roadmap sync (attended) | Weekly | 2 team reps | 45 min | 48 | 72 (attended-time only) |
| X2 | Company all-hands (attended) | Monthly | 9 | 60 min | 12 | 108 (attended-time only) |
| X3 | Platform EMs' guild (attended) | Monthly | 1 (manager) | 60 min | 12 | 12 (attended-time only) |

Capacity: `9 × 2,000 = 18,000` person-hr/yr. Owned-ritual load:
`1,971 / 18,000 ≈ 11.0%`. With attended rituals: `2,163 / 18,000 ≈ 12.0%`.
Just above the ~10% comfortable band from Chapter 2; the AI-tools-sync
line stands out as the largest single owned cost at 432 person-hr/yr.

### 2.2 Per-ritual audit template (step 3–4)

Filled template for each owned ritual. Only four are shown here in full;
the complete artifact contains all twelve (see the Chapter 2 example for
the shape — repeat per ritual on the team's wiki page).

**Ritual 1 — Standup.**

| Field | Value |
|---|---|
| Cadence | Daily (M–Th), 09:30, 10 min |
| Lencioni type | Daily check-in |
| Attendees | 9 (whole team) |
| Purpose | Synchronise within-day — blocker visibility, not problem solving |
| Defended tile | WIP count; blocker-age (mod-104 Ch. 4 flow metrics) |
| Signal-of-decay | Average duration > 15 min for 2 consecutive weeks; blockers surface in Slack before standup rather than at it |
| Owner | Tech lead |
| Last audit | 2026-11-13 (this audit) — kept |

**Ritual 4 — Retrospective.**

| Field | Value |
|---|---|
| Cadence | Biweekly, Friday 15:00, 60 min |
| Lencioni type | Monthly strategic (running biweekly is defensible for inspection-and-amendment) |
| Attendees | 9 (whole team) |
| Purpose | Inspect and amend the practice — rituals, workflows, review standards |
| Defended tile | Amendment land-rate (count of retrospective-driven amendments merged per quarter); repeat-incident rate (mod-109) |
| Signal-of-decay | Three consecutive retros with no amendments shipped; same theme repeats without action |
| Owner | Manager (facilitator rotates) |
| Last audit | 2026-11-13 — kept |

**Ritual 8 — AI tools sync.**

| Field | Value |
|---|---|
| Cadence | Weekly, Wednesday 11:00, 60 min |
| Lencioni type | Attempted monthly strategic, running as weekly tactical |
| Attendees | 9 (whole team) |
| Purpose | Cross-pollinate AI-augmented workflow learning |
| Defended tile | Novel-workflow adoption rate (mod-106); stage progression (mod-105) |
| Signal-of-decay | Four consecutive weeks of weak content; stage distribution stable two quarters; attendance < 70% |
| Owner | Enablement champ |
| Last audit | 2026-11-13 — **flagged** (see §2.3) |

**Ritual 11 — Governance retrospective.**

| Field | Value |
|---|---|
| Cadence | Semiannual, 90 min |
| Lencioni type | Quarterly off-site (run semiannually) |
| Attendees | 9 + AI-Governance peer |
| Purpose | Inspect mod-110 gap register, close loops, confirm routing posture |
| Defended tile | Gap-register closure rate; routed-upward trigger land-time |
| Signal-of-decay | Gap register grows without closure for 2 consecutive retros |
| Owner | Manager (co-signed with governance peer) |
| Last audit | 2026-11-13 — kept |

### 2.3 Flagged rituals, decisions (step 5–6)

Three rituals are flagged. For each, a decision is made in the open.

**Flag A — AI tools sync (Ritual 8).** Content thinned over the last
quarter: four of the last eight sessions have been repeat demos, vendor
release-note walk-throughs, or general-chat. Stage distribution has been
stable at Stage 3–4 across all ICs for two quarters per the `mod-105`
ladder. Defended tiles (novel-workflow adoption, stage progression) have
not moved.

- **Decision:** right-size. Retire the weekly slot; replace with a
  monthly 45-minute CoP session owned by the enablement champ, purpose:
  one novel-workflow demo per session, rotating demo owner.
- **Capacity reclaimed:** `432 − (9 × 0.75 × 12) = 432 − 81 = 351`
  person-hr/yr.

**Flag B — Dashboard review (Ritual 6).** Weekly 30-min; the last five
sessions produced no action items. The defended tile (variance against
expected) is moving on a longer cadence than weekly — DORA signals and
DX Core 4 updates land on a biweekly / monthly rhythm.

- **Decision:** right-size. Move to biweekly; shorten to 25 min; require
  that every session produce at least one named amendment or logged
  "no action warranted, here is why." Three consecutive no-action
  sessions flip the ritual to monthly.
- **Capacity reclaimed:** `120 − (5 × 0.42 × 24) = 120 − 50 = 70`
  person-hr/yr.

**Flag C — Sprint review / demo (Ritual 3).** Attendance has thinned to
5/9 for the last six sessions; Product and Design partner attendance has
dropped to zero over the last two months. The slot is executing more as
a status update than as a stakeholder alignment ritual.

- **Decision:** split. Keep a 20-minute internal demo at the end of
  each sprint (purpose: within-team visibility of what shipped). Move
  the stakeholder-facing demo to a *monthly* 45-minute release review
  with Product and Design re-committed to attend; owned jointly with
  the PM (Chapter 3 grid co-sign).
- **Capacity reclaimed:** `176 − (9 × 0.33 × 26) − (11 × 0.75 × 12)
  = 176 − 77 − 99 ≈ 0`, but *attendance quality* is the recovered
  value, not raw hours. The ritual now defends its tile.

**Not flagged, but noted.** The weekly standup (Ritual 1) sometimes
drifts to 15 minutes. The audit notes this as a drift-watch, not a flag;
the owner commits to enforce the 10-minute discipline before escalating.

### 2.4 Decision-log entries (step 7)

One decision-log entry per material change, in Chapter 5's ADR-style
template. The AI tools sync entry is shown in full as a worked example;
the dashboard-review and demo-split entries follow the same shape.

```markdown
# DL-2026-11-13-001 — Retire the weekly AI tools sync; replace with monthly CoP

Date: 2026-11-13
Author: <team manager>
Status: Active
Scope: Team ritual calendar

## Context

The team is at Stage 3–4 across all ICs per the mod-105 ladder. The
weekly AI tools sync has thinned to repeat content over the last four
sessions; the defended tile (novel-workflow adoption rate) has been
stable for two quarters without the ritual moving it. Annual cost of
the slot: 432 person-hr/yr — the largest single owned-ritual cost on
the calendar.

## Decision

Retire the weekly AI tools sync on 2026-11-20. Replace with a monthly
45-minute Community-of-Practice session owned by the enablement champ,
purpose: one novel-workflow demo per session, rotating demo owner.

## Rationale

(1) Per the Chapter 2 audit template, the ritual is no longer defending
its tile. (2) ~351 person-hr/yr of maker time is reclaimed. (3) Monthly
cadence matches the current stage distribution of the team; the
cross-pollination function is still served at a cadence appropriate to
the pace of novel-workflow emergence.

## Alternatives considered

(a) Keep weekly, cut to 30 min — rejected: duration is not the issue,
the content is. (b) Retire outright with no replacement — rejected:
stage distribution could regress if the CoP forum disappears entirely.
(c) Bi-weekly cadence — rejected on initial pass; held as a fallback
if monthly turns out too thin.

## Reversal conditions

If novel-workflow adoption rate (mod-106 tile) drops >15% in Q1 next
year, revisit with potential bi-weekly cadence. If stage distribution
regresses by one stage for ≥2 ICs, revisit.

## Review date

2027-02-13 (90 days). Quarterly spot audit thereafter.

## Links

- Chapter 2 audit template — ritual #8
- Replaced ritual's historical attendance log
- Enablement champ's CoP charter (new)
- Mod-105 stage ladder dashboard
```

The dashboard-review and demo-split decisions produce DL-2026-11-13-002
and DL-2026-11-13-003 in the same template. Minor cadence tweaks (e.g.,
standup drift-watch) do not warrant decision-log entries; a one-line
calendar comment is enough.

### 2.5 Refreshed calendar (step 8–9)

Post-audit calendar, total cost:

| # | Ritual | Pre-audit cost | Post-audit cost | Δ |
|---|---|---|---|---|
| 1 | Standup | 288 | 288 | 0 |
| 2 | Sprint planning | 234 | 234 | 0 |
| 3 | Sprint internal demo (new shape) | 176 | 99 | −77 |
| 3b | Monthly release review (new, joint with PM) | — | 99 | +99 |
| 4 | Retrospective | 234 | 234 | 0 |
| 5 | 1:1s | 192 | 192 | 0 |
| 6 | Dashboard review (biweekly) | 120 | 50 | −70 |
| 7 | Office hours | 144 | 144 | 0 |
| 8 | Monthly CoP (replaces AI tools sync) | 432 | 81 | **−351** |
| 9 | Eval-set review | 48 | 48 | 0 |
| 10 | Tool-registry review | 40 | 40 | 0 |
| 11 | Governance retro | 27 | 27 | 0 |
| 12 | Incident retro | 36 | 36 | 0 |
| | **Owned subtotal** | **1,971** | **1,572** | **−399** |

Capacity reclaimed: **~399 person-hr/yr**. Owned-ritual load drops from
~11.0% to ~8.7% of team capacity — back inside the comfortable band.

### 2.6 Next audit and off-cycle triggers (step 10)

- **Annual full audit:** 2027-11 (same quarter next year).
- **Quarterly spot audit:** at the next quarterly off-site (2027-02),
  review the three rituals changed in this audit plus any ritual added
  in the interim.
- **Event-triggered audit:** headcount change of ±2; major scope change
  (new product line or sunset); a process incident where a decision got
  missed because a ritual did not surface it.

## 3. Validation steps

Before publishing the refreshed calendar and closing the audit:

1. **Did the audit produce at least one retirement or right-sizing?**
   If not, re-run skeptically — the discipline is binding, not advisory.
2. **Does every surviving ritual have a one-sentence defended tile and
   a one-sentence signal-of-decay?** Rituals without either are judgement
   calls; the judgement-call sentence is recorded against them.
3. **Is every ritual classified against one of Lencioni's four types
   cleanly?** A ritual "between" two types is a split candidate.
4. **Was the owned-ritual load compared to team capacity?** Teams at
   15–20% are overspent; teams at 25%+ are meeting-augmented, not
   AI-augmented. The number is on the audit sheet, as a %, not just as
   hours.
5. **Does every material change have a decision-log entry in ADR
   format?** Minor tweaks (standup drift-watch) do not need one;
   retirements, cadence shifts of established rituals, and splits do.
6. **Were cross-functional rituals co-signed with the partner?** The
   demo split above is a joint decision with the PM; the governance
   retro co-signs with the governance peer. Unilateral retirement of a
   joint ritual is out of scope for the audit.
7. **Is the capacity-reclaimed number recorded?** The person-hr/yr saved
   is a credible line in the next quarterly pre-read's productivity
   section (Exercise 03).
8. **Is the next audit date on the calendar, and are the off-cycle
   triggers written down?**

## 4. Rubric

Grade a submitted audit + decision-log entries. Each criterion 0–3.
Pass bar: **20 / 30**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Inventory is complete** | Fewer than half the recurring rituals listed | Most listed; cadence missing | All owned rituals listed with cadence and attendees | Owned rituals + attended rituals (costed as attended-time only) |
| R2 | **Annual cost computed per ritual and in total** | Not computed | Partial | All rituals costed; total absent | Per-ritual cost + total + % of team capacity + comparison against the ~10% band |
| R3 | **Lencioni classification applied** | Missing | Applied inconsistently | Applied to every ritual | Applied cleanly; overloaded rituals flagged for split |
| R4 | **Defended tile per ritual** | Missing | Generic ("improves productivity") | Specific tile named for most rituals | Specific tile per ritual + signal-of-decay sentence |
| R5 | **At least one retirement / right-sizing / split** | None | One trivial change | One material change with rationale | Multiple material changes; the hardest ritual to retire is addressed |
| R6 | **Decision-log entries in ADR format** | Absent | Prose decisions, no template | Template partially filled | Context / decision / rationale / alternatives / reversal conditions / review date / links for every material change |
| R7 | **Cross-functional rituals handled correctly** | Retired unilaterally | Partner informed after the fact | Partner consulted | Partner co-signs; the change links the Chapter 3 grid |
| R8 | **Capacity reclaimed is computed and recorded** | Absent | Hand-wave | Number present | Number present + routed to the next quarterly pre-read's productivity section |
| R9 | **Next-audit cadence and off-cycle triggers named** | Absent | Annual only | Annual + quarterly spot | Annual + quarterly spot + named event-triggers (±2 headcount, scope change, process incident) |
| R10 | **Judgement-call rituals surfaced honestly** | Hidden | Mentioned | Mentioned with one-sentence justification | Mentioned with justification + revisit trigger (e.g., "if partner attendance drops below 2/4 sessions") |

## 5. Common mistakes

- **Revalidating everything.** An audit that keeps all twelve rituals
  because "they feel useful" has not applied the defended-tile test.
  The test is binding: if the tile has not moved and will not get worse
  when the ritual stops, the ritual is drift.
- **Classifying a weekly tactical as "a long standup."** Standups are
  daily check-ins (~10 min). A 60-minute weekly meeting is a tactical;
  call it that and own its purpose, or re-scope.
- **Using the retrospective as a strategy forum.** Retros inspect and
  amend; strategy belongs to the quarterly off-site. Overloading the
  retro produces neither good inspection nor good strategy.
- **Dashboard-reading club.** A dashboard review that stares at tiles
  without producing amendments is not defending the dashboard tile; its
  defended tile is "variance against expected", and the signal-of-decay
  is "three consecutive reviews with no action items." Enforce the
  action-item discipline or right-size.
- **Standardising 1:1 cadence in the audit.** 1:1s are per-direct-report
  choices informed by growth plans (`mod-107`), not team-calendar policy.
  The audit costs them but does not dictate cadence.
- **Retiring a cross-functional ritual unilaterally.** The ritual the
  partner co-owns cannot be retired by one side. Chapter 3 grid first;
  co-sign in the decision log.
- **Omitting the capacity-reclaimed number.** The number is credible
  input to the next pre-read. Teams that leave it out lose the
  compounding credit audits earn over quarters.
- **Treating the audit as a one-off.** The calendar drifts. The audit
  cadence (annual full + quarterly spot + event-triggered) is itself a
  calendar entry.
- **Scoring K-tile claims from gut feel.** The tile must be a real tile
  from the `mod-104` dashboard, the `mod-109` incident index, or the
  `mod-110` gap register — not invented for the audit. A ritual whose
  defended tile does not exist in the measurement framework is a ritual
  defending nothing.
- **Hiding the judgement-call rituals.** A ritual kept because "the
  team likes it" is allowed in the open with a one-sentence note. A
  ritual kept without acknowledging the absence of a defended tile
  erodes the audit's credibility.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework)
  — the **Govern** function's emphasis on *roles, responsibilities, and
  accountability structures*, and the **Measure** function's insistence
  on *linking activities to measurable outcomes*, are the structural
  reasons every ritual is required to name an owner, a defended tile,
  and a signal-of-decay. The **Manage** function's reference to
  *regularly reviewing and adjusting* underwrites the annual-plus-spot
  audit cadence.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/)
  — the incident-retro ritual's defended tiles (incident count by class,
  regression-fixture land-rate) are a team-scope instance of the
  risk-tracking posture OWASP recommends for ML systems. The audit
  ensures that risk-facing rituals do not drift silently.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat taxonomy
  referenced by the governance retro's gap-register inputs. When a
  ritual defends a tile that is itself an ATLAS-derived risk category
  (e.g., prompt-injection incident rate), the audit confirms the tile
  is still being moved by the ritual rather than by chance.

<!-- follow-up: once teams have run two annual audits, add a worked
example of a second-year audit so managers can see what "the audit
caught the drift that re-emerged" looks like — the compounding value of
running the audit on cadence rather than once. -->
