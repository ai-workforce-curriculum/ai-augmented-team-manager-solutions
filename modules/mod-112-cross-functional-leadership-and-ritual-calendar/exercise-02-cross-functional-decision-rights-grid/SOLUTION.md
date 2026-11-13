# Exercise 02 — Cross-Functional Decision-Rights Grid

> Module: `mod-112-cross-functional-leadership-and-ritual-calendar`
> Exercise type: **design** (worked grids, drafting protocol, grading rubric)

## 1. Solution overview

The deliverable is **v0.1 of the team's cross-functional decision-rights
grid**, populated for each of the five first-order partner functions at
L40: Product, Platform / DevEx, Design, Data / Analytics, and the
AI-Governance peer. Each grid is a compact RAPID-style table — Recommend,
Agree, Perform, Input, Decide — plus a sixth column naming the ritual on
the Chapter 2 calendar that executes the decision. Each grid is drafted
*with* the named partner in a 30-minute conversation, co-signed, and
published at a canonical URL linked from the team one-pager (Chapter 5).

The grid is the space-layer of the team operating system (Chapter 7): it
tells the team and the organisation where joint decisions get made. A
grid the partner has not seen is a wish-list; a grid the partner has
co-signed is a contract. The 30-minute conversation per partnership is
the load-bearing step of this exercise.

The solution encodes five design choices the grid will be graded against:

1. **One Decide slot per row.** Joint-decide invites retroactive
   disagreement. When two functions are "both deciding", one is the
   tie-breaker — usually the function whose scope is more affected.
   Write it down.
2. **Every row names the ritual that executes it.** If no ritual on the
   Chapter 2 calendar fits, the grid row is wrong or the calendar is
   missing a ritual. Either way, the gap is resolved before the grid
   ships.
3. **The grid is co-signed.** Drafted jointly with the partner, not
   handed over by the manager. The Chapter 3 drafting conversation is
   where the partner's unspoken expectations get surfaced.
4. **A grid row is a *decision*, not a conversation.** Standing
   coordination syncs are rituals on the calendar; the decisions made
   at them are grid rows. The grid does not list every topic a
   partnership touches.
5. **The grid is dated and lives in the decision log.** Partnership
   structure drifts. When it drifts, the shift is a dated decision with
   rationale, alternatives, and reversal conditions — not an invisible
   evolution.

## 2. Implementation — worked grids for "Underwriting-assist team" (fictional calibration)

Nine-person team (manager + 8 ICs), ML-assisted underwriting. The five
named partners:

- **Product:** J. Smith (consumer-credit PM).
- **Platform / DevEx:** L. Chen (shared-platform EM, L50 — co-equal
  peer not available at L40 in this organisation; see §2.6 note).
- **Design / Design Systems:** R. Okafor (design-system lead; the team
  gets fractional time from a central design-systems team).
- **Data / Analytics:** M. Patel (senior analytics engineer, L40 peer).
- **AI-Governance peer:** S. Mwangi (`ai-risk-engineer`, underwriting
  cluster, L25 — the engineered-risk relationship from `mod-109` Ch. 5
  is the grid's basis in the absence of a co-equal governance manager).

### 2.1 Product partnership grid

Six decision types enumerated from the last six months of joint
decisions. Signed with J. Smith after a 30-minute drafting conversation
on 2026-11-10.

| Decision type | Recommend | Agree | Perform | Input | Decide | Ritual |
|---|---|---|---|---|---|---|
| Quarter roadmap shape | PM + Manager (joint) | Design, Data | Team | Platform, Governance | **VP Product** (above threshold); below threshold → PM | Quarterly roadmap review (off-site) |
| Mid-sprint priority swap | PM | Manager | Team | — | **PM** | Weekly roadmap sync |
| Capacity allocation: features vs platform health | Manager | PM | Team | — | **Manager** (VP Eng sign-off > 30% platform) | Quarterly off-site |
| Outcome-metric definition for a shipped feature | Data + PM (joint) | Manager | Data | Design | **PM** | Monthly data sync |
| Release go/no-go (standard-risk) | PM + Manager | Governance (if workflow classed high-risk) | Team | Design, Platform | **PM** | Monthly release review |
| AI-adoption budget: features vs enablement | Manager | PM | Team | — | **Manager** (within team-scope envelope) | Quarterly off-site |

Notes from the drafting conversation (recorded for the decision-log
entry, not on the grid itself):

- J. Smith pushed back on the original draft, which had "Release
  go/no-go" decided jointly. Agreed that the PM owns outcome-facing
  go/no-go and the manager owns delivery-risk go/no-go; where they
  differ, Governance arbitrates. The "high-risk workflow" carve-out is
  the escalation path to Governance.
- Added "AI-adoption budget" row. The original draft was missing it;
  J. Smith named it as a decision that had been made implicitly and
  belonged on the grid.

### 2.2 Platform / DevEx partnership grid

Five rows. Signed with L. Chen (L50 Platform EM) after a 30-minute
conversation on 2026-11-11. The co-equal-peer caveat is noted on the
grid preamble (see §2.6).

| Decision type | Recommend | Agree | Perform | Input | Decide | Ritual |
|---|---|---|---|---|---|---|
| Agent-runtime standardisation (org-wide) | Platform EM | Manager (team-scope input) | Platform | Team, Governance | **Platform EM** | Monthly platform sync |
| Team-scope tool-registry exceptions | Manager | Platform EM | Team | Governance | **Manager** (if inside team-scope envelope); Platform EM if policy conflict | Quarterly tool-registry review |
| DX Core 4 instrumentation of team workflows | Platform EM + Data | Manager | Platform + Data | Team | **Platform EM** (instrumentation definition); Manager (opt-in cadence) | Monthly platform sync |
| Eval-harness standardisation | Platform EM | Manager, Governance | Platform | Team | **Platform EM**, but team retains team-specific evals per `mod-103` | Quarterly platform review |
| Shared-infra breaking change affecting team workflow | Platform EM | Manager | Platform | Team | **Platform EM** (deadline); Manager (migration-plan content) | Event-triggered sync |

Notes:

- The grid records what is already true: Platform / DevEx at this
  organisation is multi-team-scoped, and the L40 manager's decision
  rights are mostly local-exception rights. The grid makes that
  explicit rather than leaving it implicit.
- "Team retains team-specific evals per `mod-103`" is a boundary line
  the team wanted written down — a prior conflict resolved.

### 2.3 Design partnership grid

Four rows. Signed with R. Okafor after a 30-minute conversation on
2026-11-12. Design is fractional (central design-systems team); the
grid reflects the lightweight partnership shape rather than inventing
weight that is not there.

| Decision type | Recommend | Agree | Perform | Input | Decide | Ritual |
|---|---|---|---|---|---|---|
| Design-system pattern approval (confidence surfaces, HITL affordances) | Team (draft pattern) | Manager | Team + Design | PM, Governance | **Design-system lead** | Monthly pattern review (or event-triggered for novel surfaces) |
| Article 50 transparency UI shape | Manager + Governance | Design-system lead | Team | PM | **Governance peer** (compliance); Design-system lead (pattern) | Event-triggered (per feature) |
| Interim pattern use when design-system has not approved | Manager | Design-system lead | Team | PM | **Design-system lead** (approves time-boxed interim); if declined → de-scope | Event-triggered |
| Accessibility sign-off on AI-interaction surfaces | Team | Design-system lead | Team | Governance | **Design-system lead** | Pre-release review |

Notes:

- R. Okafor flagged a dormant row (dashboard-UI AI pattern) that the
  team drafted but Design has not reviewed. It is on the grid as a
  dormant row, marked with its drafted-not-approved status, to be
  reviewed at the next pattern-review ritual.

### 2.4 Data / Analytics partnership grid

Five rows. Signed with M. Patel after a 30-minute conversation on
2026-11-12.

| Decision type | Recommend | Agree | Perform | Input | Decide | Ritual |
|---|---|---|---|---|---|---|
| Canonical metric definition (what counts as a "merged PR with AI assist") | Data + Manager (joint) | PM, Platform | Data | Team | **Data lead** (canonical defn); Manager (team-scope divergence recorded) | Monthly data sync |
| Dashboard-tile authoring for team-scope narrative | Manager | Data | Data | Team | **Manager** (team-scope tile); Data (cross-team comparability) | Biweekly dashboard review |
| Instrumentation of new workflow (team request) | Manager | Data | Data + Team | PM | **Data lead** (priority); Manager (within team-scope prioritisation) | Monthly data sync |
| Cross-team metric divergence reconciliation | Manager | Data | Data | Peer teams | **Data lead** (when canonical defn at stake); Manager (when team context explains divergence) | Monthly data sync |
| Dashboard retirement / replacement | Data | Manager | Data | Team | **Data lead** | Quarterly data review |

### 2.5 AI-Governance partnership grid

Six rows. Signed with S. Mwangi (`ai-risk-engineer`) after a 30-minute
conversation on 2026-11-13.

| Decision type | Recommend | Agree | Perform | Input | Decide | Ritual |
|---|---|---|---|---|---|---|
| Workflow risk classification (in-scope / high-risk) | Team (initial) | Manager | Risk engineer | Governance architect (if escalation) | **Risk engineer** (classification); escalates to Governance architect at threshold | Packet sync (quarterly) |
| Eval-commissioning brief for a newly-scoped workflow | Risk engineer + Team | Manager | Team | Governance architect | **Risk engineer** | Event-triggered |
| Incident-retro root-cause routing (team vs engineered) | Manager | Risk engineer | Team + Risk engineer | — | **Risk engineer** (engineered root-cause); Manager (team-scope root-cause) | Per-incident retro |
| Gap-register entry creation / closure | Manager | Risk engineer | Team | Governance architect | **Manager** (team-scope entries); Risk engineer (joint entries); Governance architect (routed-upward) | Governance retro + ad-hoc |
| Article 14 workflow-sheet drafting | Manager + Team | Risk engineer | Team | Governance architect, Counsel | **Risk engineer** (adequacy); Manager (workflow content) | Event-triggered |
| Policy-triggered workflow amendment | Risk engineer | Manager | Team | Governance architect | **Manager** (team-scope implementation); Risk engineer (compliance adequacy) | Packet sync |

Notes:

- Boundary stress-tested against an actual recent decision: the
  "incident-retro root-cause routing" row was validated by walking the
  2026-09 workflow-regression incident; the retro landed with
  engineered root-cause (S. Mwangi), and the handoff worked. The grid
  predicts the actual path.

### 2.6 Why one row has an L50 counterpart

The Platform / DevEx grid's counterpart is an L50 Platform EM, not an
L40 peer. This is normal: Platform / DevEx often does not have a
co-equal peer at team scope in mid-sized organisations. The grid remains
a peer-partnership artifact even when the levels differ; what changes is
the recognition that most Decide slots on org-wide platform decisions
sit with the L50 (correctly so), and the manager's team-scope rights
are mostly local-exception rights.

### 2.7 The drafting protocol per partnership

The 30-minute conversation per partnership follows a consistent shape:

1. **Open with the draft.** Share the pre-drafted grid (not a blank
   template) 24 hours before the conversation. The partner reads it
   cold; the conversation is the amendment.
2. **Walk the rows.** Per row: does the decision type belong on the
   grid? Is the Decide slot right? Does the named ritual actually
   execute it? Does the cadence match reality?
3. **Elicit missing rows.** "What decisions do you expect we will make
   together that are not on this grid?" is the load-bearing question.
   Partners always surface rows the manager missed.
4. **Elicit non-rows.** "What decisions on this grid should actually
   be single-function, not joint?" — the opposite failure mode: a grid
   that over-reaches into the partner's domain.
5. **Resolve Decide conflicts explicitly.** When the partner pushes
   back on a Decide slot, name the tie-breaker rule ("scope most
   affected"; "function that bears the cost of being wrong"). Do not
   leave it fuzzy.
6. **Close on the published artifact.** The partner will read the
   signed grid once published. The conversation ends when both parties
   would commit their name to it.

### 2.8 Reconciling with the re:Work behavioural baseline

The grids above are *structural*. Google's Project Oxygen baseline is
*behavioural*. Check the composed shape against the four outward-facing
behaviours:

- **Is a good communicator.** The grids make the decisions legible;
  the rituals that execute them are the communication channels. The
  "notes from the drafting conversation" artifact per partnership is
  the communication audit trail.
- **Collaborates across.** Every grid row names a cross-functional
  ritual, not a manager-unilateral decision.
- **Is a good coach.** The office-hours ritual on the calendar
  (Chapter 2) is not a grid ritual, but it is where the behavioural
  coaching surface lives. The grid does not replace it.
- **Has key technical skills.** The grid presupposes the manager can
  participate credibly with Data on metric definitions, with Design on
  patterns, and with Governance on the gap register. If the grid rows
  read as pass-throughs to the partner, the manager is under-equipped
  — see Exercise 05 (certifications portfolio) for the vocabulary
  layer that closes this.

### 2.9 Decision-log entries

One decision-log entry per partnership grid, in Chapter 5's ADR-style
template. The Product entry is shown in full as a worked example; the
other four follow the same shape.

```markdown
# DL-2026-11-10-001 — v0.1 Product-partnership decision-rights grid

Date: 2026-11-10
Author: <team manager>, J. Smith (Product)
Status: Active
Scope: Team cross-functional partnerships (Product)

## Context

The team and Product have made six material joint decisions in the last
six months without a written structure; two of them went through a
crisis Zoom because no ritual owned the decision. Chapter 3 calls for a
decision-rights grid co-signed with the partner before the next cycle.

## Decision

Six-row Product-partnership grid signed as of 2026-11-10. See
`docs/partnerships/product-grid.md` for the canonical version.

## Rationale

Last six months' joint decisions were enumerated; each has a row.
Decide slots were negotiated in a 30-minute conversation; where the
partner pushed back, the tie-breaker rule is recorded (scope most
affected, or function bearing the cost of being wrong). Every row
names a ritual on the Chapter 2 calendar that executes it.

## Alternatives considered

- Draft unilaterally and circulate for comment — rejected: produces a
  wish-list, not a contract; partner engagement would be shallow.
- Use a strict RAPID template (PDF forms per decision) — rejected: too
  heavyweight for a six-row grid; the ritual column is more useful
  than a Perform/Agree formalism applied to every decision type.
- Defer until a crisis forces it — rejected: the module explicitly
  names this as the failure mode.

## Reversal conditions

If a quarter passes with no decisions landing cleanly in the predicted
ritual, revisit the grid. If a new decision type emerges twice in two
quarters, add a row and co-sign.

## Review date

2027-11-10 (annual retro). Off-cycle trigger: any Decide-slot dispute
that escalates above the two signers.

## Links

- `docs/partnerships/product-grid.md`
- Chapter 2 ritual calendar (co-referenced per row)
- Exercise 01 audit (post-audit calendar is the grid's ritual surface)
- `mod-102` roadmap (decided in several grid rows)
```

### 2.10 Validation against a recent decision

Pick one joint decision made in the last three months and walk the
grid. Did the decision land in the ritual the grid predicts? Was the
Decide role the one named?

Worked check: the 2026-09 "AI-adoption budget: features vs enablement"
decision was made at the Q3 quarterly off-site, with the manager
deciding and the PM agreeing (within the team-scope envelope); it
matches row 6 of the Product grid. If the decision had instead been
made in a crisis Zoom the day before the next sprint, either the grid
is wrong (amend) or the behaviour is wrong (fix). A grid that matches
the actual decision path is a grid that is not fiction.

### 2.11 RAPID-coverage sanity check (stretch)

Count Decide slots per named person across all five grids:

| Decide holder | Rows | % of 26 total |
|---|---|---|
| PM (J. Smith) | 4 | 15% |
| Platform EM (L. Chen) | 5 | 19% |
| Design lead (R. Okafor) | 4 | 15% |
| Data lead (M. Patel) | 5 | 19% |
| Risk engineer (S. Mwangi) | 4 | 15% |
| **Manager** | 4 | 15% |

No single role holds > 50%. If the manager held Decide on 15 of 26
rows, the grid would be a sign the partner drafting conversations were
nominal; the distribution here is consistent with a genuine partnership
map, not a unilateral one.

## 3. Validation steps

Before publishing the five grids and closing the exercise:

1. **Does every row have exactly one Decide name?** Joint-decide is a
   smell; name the tie-breaker rule.
2. **Does every row name a ritual on the Chapter 2 calendar?** Calendar
   gaps are closed before the grid ships (add the ritual via Exercise 01
   discipline, or reclassify the decision as handled off-calendar with
   explicit cadence rules).
3. **Did each partner spend 30 minutes with the draft?** The 30-minute
   conversation is the load-bearing step. A grid the partner "approved
   over Slack" without reading the rows is not a signed grid.
4. **Is the Decide distribution sane?** No single role holding > 50% of
   Decide rows across all five grids. A manager-dominated distribution
   signals the partner conversations were nominal.
5. **Were dormant rows surfaced honestly?** A grid row that predicts a
   decision not yet seen is okay if marked dormant and reviewed at the
   annual retro. Hiding dormant rows erodes the grid's credibility.
6. **Were boundary rows that cross two partnerships resolved?** A
   tooling decision touching Platform / DevEx *and* AI-Governance has
   been walked with both partners; the Decide slot is resolved rather
   than fuzzy.
7. **Was at least one recent decision validated against the grid?** If
   the grid predicts a different path than what actually happened, the
   grid or the behaviour is amended before v0.1 ships.
8. **Is a decision-log entry per partnership recorded?** Context,
   decision, rationale, alternatives, reversal conditions, review date,
   links — one entry per grid.

## 4. Rubric

Grade a submitted set of grids + decision-log entries. Each criterion
0–3. Pass bar: **20 / 30**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **All five partnerships covered** | Fewer than three | Three or four grids, but incomplete | All five present, 4–8 rows each | All five present, 4–8 rows, plus notes on absent / fractional partners |
| R2 | **Decide slots are singular and defensible** | Multiple Decide names in rows | Most rows singular | All rows singular | Singular + tie-breaker rule recorded where contested |
| R3 | **Every row names a ritual** | Rituals missing | Some rows have rituals | All rows have rituals | All rows have rituals + calendar gaps closed via Exercise 01 |
| R4 | **Partner-drafting discipline followed** | Grid drafted unilaterally | Drafted, then shared | Drafted, then 30-min conversation per partnership | 30-min conversation + notes recorded for the decision log |
| R5 | **Decision-log entry per grid** | Absent | Prose decision, no template | Template partially filled | Context / decision / rationale / alternatives / reversal conditions / review date / links for every grid |
| R6 | **Re:Work behavioural check** | Not mentioned | Mentioned | Checked against four outward behaviours | Checked + specific gaps named (e.g., "coaching surface lives in office hours, not the grid") |
| R7 | **Validation against a recent decision** | Not run | Run informally | Run with one decision | Run; if mismatch found, grid or behaviour amended |
| R8 | **Dormant rows surfaced** | Hidden | Mentioned | Marked dormant with annual-review commitment | Marked + reversal condition for dormancy ("retire if no decision in two consecutive quarters") |
| R9 | **RAPID-coverage sanity check** | Not run | Run, no action | Run; distribution recorded | Run; distribution recorded; if > 50% centralisation, discussed with partners |
| R10 | **Cross-boundary rows resolved** | Fuzzy | Mentioned | Walked with one partner | Walked with both partners; Decide slot resolved explicitly |

## 5. Common mistakes

- **Drafting unilaterally and calling it a grid.** The manager's
  preferences are not a partnership contract. The 30-minute
  conversation is where the partner's unspoken expectations get
  surfaced; skipping it produces a wish-list.
- **Two names in the Decide column.** Joint-decide is a smell. In
  practice one will decide; the other will be surprised. Name the
  tie-breaker rule, even if it's "the function more affected scope-wise."
- **Listing conversations, not decisions.** A grid row like
  "coordinate on backlog" is not a decision; it is a conversation. The
  decisions made at the coordination ritual are the grid rows.
- **Manager decides everything.** The grid where the manager holds the
  Decide slot on every row was drafted without the partner. Partnership
  decisions in the partner's domain belong to the partner.
- **Partner decides everything they touch.** The opposite failure. The
  manager holds Decide on team-internal workflow decisions even when
  the partner is interested (and has Input).
- **No ritual column.** A decision with no ritual is a decision with no
  room; it is made in Slack in a crisis. Every row names its executor.
- **One ritual per row.** The quarterly roadmap sync may execute 3–5
  Product-partnership rows; one ritual per row is over-structuring.
- **Dormant rows hidden.** A row that has not fired in six months is
  fine if marked dormant and reviewed annually; pretending the grid is
  fully-exercised erodes trust.
- **Boundary rows left fuzzy.** A decision that crosses two
  partnerships (tooling touching Platform *and* Governance) has been
  walked with both partners. Fuzzy boundaries fail under pressure.
- **No validation against reality.** The grid that predicts the wrong
  path for a recent real decision is fiction. Validate; amend; ship.
- **No revision cadence.** Partnership structure drifts. The annual
  retro + event-triggered amendments are themselves on the calendar.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework)
  — the **Govern** function names *roles and responsibilities* and
  *accountability structures* as prerequisites for every other function.
  The decision-rights grid is the team-scope instance of that
  prescription: every joint decision has a named Decide role, a named
  ritual, and a dated record. The **Map** function's attention to
  *context and third-party dependencies* motivates the explicit inclusion
  of the AI-Governance peer and Platform / DevEx as grid partners, not
  as implicit upward escalation paths.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/)
  — the risk categories (data poisoning, model inversion,
  training-serving skew) are what the AI-Governance peer's grid rows
  are defending. A grid without a Governance-peer partnership is a
  grid leaving those risk categories ungoverned at team scope.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat
  taxonomy. The "workflow risk classification" row of the Governance
  grid uses ATLAS categories (and NIST AI RMF sub-categories) as the
  shared vocabulary for classifying a workflow as in-scope or
  high-risk, rather than inventing a local scheme.

<!-- follow-up: once teams have operated a grid for two quarters, add a
worked "annual retro" example showing which rows fired, which stayed
dormant, which got amended, and what the partner-health survey revealed.
This is where the grid proves it is a contract, not a snapshot. -->
