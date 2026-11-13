# Exercise 03 — Exec Pre-Read Connecting Roadmap to ROI to Governance

> Module: `mod-112-cross-functional-leadership-and-ritual-calendar`
> Exercise type: **design** (worked pre-read, composition discipline, grading rubric)

## 1. Solution overview

The deliverable is **v0.1 of the team's quarterly exec pre-read**: a 4–8
page artifact that composes the team's eleven backing artifacts
(`mod-101` through `mod-111`) into one coherent narrative for the direct
manager and the governance program consumer, with a single spine, one
explicit ask, and the three-reviewer honesty discipline run before
leadership reads it.

The pre-read is the upward-composition layer of the team operating
system (Chapter 7). It replaces two failure modes: dashboards-as-narrative
(the manager forwards a URL and the VP infers a story that may not
match the manager's) and four-separate-reports syndrome (the VP reads
four clean reports and composes them incoherently). Composition is the
pre-read's real work.

The solution encodes six design choices the pre-read will be graded
against:

1. **One spine.** Usually the roadmap-to-outcome arc. Deviate only when
   the quarter was dominated by an incident, by a markedly-moved
   productivity signal, or by a governance event. The spine is one
   thing; a pre-read with four spines is four pre-reads stapled
   together.
2. **One-line claim at the top.** The sentence the VP tells their
   manager in the hallway. Written last; written ruthlessly. If it
   cannot be written in one sentence, the pre-read has no spine.
3. **Honest driver attribution.** Productivity movement has several
   causes (roadmap shape, platform investment, enablement maturity,
   AI adoption, external factors); attributing all gains to AI is
   marketing and will be read as such within two quarters.
4. **The anti-theatre paragraph.** `mod-104` Ch. 8's qualitative-signal
   discipline is a non-negotiable section, not a rhetorical flourish.
5. **One or two asks, with deadline and consequence.** No ambush
   decisions; pre-reads formalise decisions already in discussion.
   Three asks is an agenda; prioritise.
6. **Three-reviewer discipline before leadership.** PM counterpart
   (roadmap honesty), governance peer (governance honesty), sister-team
   peer manager (structural honesty). Review is for honesty and shape,
   not for sign-off — the manager owns the pre-read.

The pre-read is explicitly **board-adjacent**, not **board-level**. Board
register at L40 is over-reach; the pre-read feeds the L60 / L70 roll-ups
rather than trying to be one.

## 2. Implementation — worked pre-read for "Underwriting-assist team" Q4 2026 (fictional calibration)

The pre-read artifact as it would appear on the team wiki. ~6 pages at
normal rendering; linked appendices are not duplicated.

---

### Executive summary

**Team:** underwriting-assist · **Scope:** ML-assisted underwriting
workflow for consumer credit (Americas) · **Period:** 2026-10-01 to
2026-12-31 · **Author:** <team manager> · **Date:** 2026-11-13 ·
**Version:** v0.1 · **Signed:** <team manager>; co-signed J. Smith
(Product), S. Mwangi (Risk engineer).

**One-line claim.** The team hit its Q4 roadmap in full, DX Core 4
time-to-ship is trending down 18% QoQ with honest attribution across
four drivers, governance posture has two open gaps routed upward, and
AI-spend is on projection — on the back of this, the ask is a
capacity-allocation decision for Q1.

**Material facts.**

- Q4 roadmap: 14 of 14 shipped on time; one swapped at week 4
  (reviewer-queue-latency fix for the shipped `review-assist` surface,
  replacing a lower-priority analytics dashboard).
- Productivity: DX Core 4 time-to-ship ↓ 18% QoQ. Attribution is
  honest across *AI assist (~40%), platform investment in CI (~30%),
  roadmap composition (~20%), external factors (~10%)* per `mod-104`
  Ch. 7 driver-attribution discipline.
- Governance: 2 incidents in period (both Class C, non-material; see
  `mod-109` retros). Gap register: 1 opened, 3 closed, 1 routed to
  governance architect.
- Spend: AI-spend $47k in quarter; 5% above prior-quarter projection;
  Q1 projection $49–52k (range, not point).
- People: stage ladder distribution: 1 at Stage 2, 5 at Stage 3, 2 at
  Stage 4; one hire closing in Q1.

### Roadmap-to-outcome arc

The quarter's roadmap committed 14 items. All 14 shipped; one was
swapped at week 4. The swap was deliberate, decided at the weekly
roadmap sync per the Product-partnership grid (Exercise 02 row:
mid-sprint priority swap, Decide = PM).

| Committed shape | What landed | Outcome tile movement |
|---|---|---|
| `review-assist` v2 surface (6 items) | All 6 shipped by week 9 | Reviewer-decision-time ↓ 22% (within target); reviewer-override rate 11% (within target band 8–15%) |
| `evidence-explain` v1 (4 items) | All 4 shipped by week 11 | Not yet measurable; first measurement window opens 2026-12-22 |
| Platform-health (3 items) | All 3 shipped | PR-queue age ↓ 14%; incident-recovery-time ↓ ~4 min median |
| Analytics tile (1 item) | Swapped for reviewer-queue-latency fix | Reviewer-queue-latency P95 ↓ from 42s → 18s; analytics tile deferred to Q1 |

The roadmap is in service of the outcome. Outcome tiles moved in the
expected direction; the swap was made to defend an outcome tile that
had started to drift.

### Productivity and AI-ROI narrative

Headline tiles from the `mod-104` dashboard (frozen 2026-12-31):

| Tile | Current | Prior Q | Δ | Expected | Variance |
|---|---|---|---|---|---|
| DX Core 4 time-to-ship | 2.3 d | 2.8 d | **−18%** | ~−10% | Faster than expected (+8 pp) |
| DORA deployment frequency | 2.1 / day | 1.8 / day | **+17%** | — | Within expected |
| DORA change-failure rate | 4.8% | 4.2% | **+14%** | ≤ 5% | Within band, trending up |
| Flow efficiency | 54% | 51% | **+5%** | — | Within expected |
| AI-cost tile (per IC-month) | $5,875 | $5,100 | **+15%** | +8% | Over-ran ~7 pp |

**Honest driver attribution for time-to-ship.** The 18% QoQ drop in
time-to-ship has four drivers; the pre-read refuses to attribute all
of it to AI:

- **AI assist (~40%).** Measured against the paired-trial subset of
  PRs with and without copilot assistance; the paired diff accounts for
  roughly 40% of the shift.
- **Platform investment (~30%).** The CI parallelisation shipped by
  Platform in Q3 lands in Q4 as a 25% reduction in CI wall-time; this
  is a Platform / DevEx driver, not a team driver.
- **Roadmap composition (~20%).** The Q4 roadmap was shaped toward
  smaller PRs (deliberate per the mid-Q3 retro); small-PR deltas
  compound into faster time-to-ship.
- **External factors (~10%).** Reduced firefighting load after the
  compliance-incident cluster closed in Q3; some tailwind.

**Anti-theatre paragraph.** The 18% improvement is real but not
exclusively ours and not uniform across workflows. Where paired PRs
with heavy AI assist are compared to similar PRs without, the AI
contribution is closer to 6–8% of time-to-ship, not 18%. The platform
investment delivered more of the quarter's gain than the AI adoption
did. Internal survey (6 of 8 ICs responding; `mod-104` Ch. 8
qualitative signal): three respondents flagged that cognitive load on
review has increased as reviewers triage more AI-authored PRs faster;
this is a decay signal for review quality that we will instrument in
Q1 via reviewer-override rate at the P90 band. Change-failure rate is
trending up (4.2% → 4.8%) and worth watching; attribution is not yet
clean (see Q1 planning).

**AI-cost over-run.** Spend over-ran the 8% projected growth by ~7 pp.
Driver: higher-than-projected context usage on the `evidence-explain`
pilot (Stage-3 ICs experimenting with longer-context prompting). The
tool-registry review (`mod-111`) flagged this; a per-workspace spend
cap is being configured for Q1.

### Governance and incident posture

**Incidents in period:** 2, both Class C (non-material).

- **2026-10-18** — prompt-injection attempted on `review-assist` chat
  surface; mitigation in place; no data exfiltration. Routed through
  blameless retro per `mod-109`. Regression fixture landed.
- **2026-11-04** — eval regression caught by nightly run; blocked
  release; root-caused to a prompt-template change not captured in the
  eval set. Eval amendment landed; fixture in place.

No Class A or B incidents in period.

**Gap register delta** (from `mod-110` register, frozen 2026-12-31):

| Movement | Count | Notes |
|---|---|---|
| Opened | 1 | New gap: Article 50 transparency surface for a Q1-planned feature; drafted, in Design review |
| Closed | 3 | Stale FAQ gaps (2); eval-set documentation gap (1) |
| Routed to governance architect | 1 | Cross-team metric-definition divergence; routed with architect's acknowledgement |

**Material policy / standard changes.** The per-workspace spend cap
being added to the tool-registry policy in Q1 is a team-scope policy
amendment; decision-log entry DL-2026-12-10-004 (see §2.4 appendices).

### Vendor and tool-registry posture

- **AI-spend:** $47k in quarter; prior quarter $44k; delta +6.8%.
  Projection for Q1: $49–52k (range reflects the per-workspace cap's
  effect being partially known).
- **Registry adds:** 0. Registry sunsets: 1 (deprecated `competitor-tool`
  per DL-2026-10-22-001). Keeps: 7.
- **Trial status:** `experimental-mcp-server` on 30-day trial;
  decision deadline 2027-01-15.

### People and enablement

- **Stage ladder distribution** (`mod-105`): Stage 2 × 1, Stage 3 × 5,
  Stage 4 × 2. One IC moved from Stage 3 → Stage 4 this quarter. No
  regressions.
- **Headcount:** 9 (manager + 8). One new hire in offer-accepted state;
  start 2027-01-27.
- **Growth / anti-atrophy signal (team-level).** Two ICs are in
  growth-plan inflection windows (`mod-107`); the CoP replacement
  ritual from Exercise 01 is designed to carry some of the atrophy-watch
  surface forward. No individual-level signal in this section per
  Chapter 4 discipline.
- **Hiring plans next period.** One IC search in flight; no further
  openings planned pending Q1 capacity decision (see ask).

### The ask

**One ask this quarter.** A capacity-allocation decision for Q1.

- **Decision requested.** Approve 25% of Q1 team capacity for the
  `evidence-explain` scale-out (vs the 15% current ask from Product).
  The delta (10 pp) comes from platform-health allocation; the trade
  is explicit and discussed with Platform EM.
- **Decision deadline.** 2027-01-10 (end of Q1 planning window).
- **Consequence of missing the deadline.** Default is the 15% Product
  ask; `evidence-explain` scale-out pushes to Q2, which moves the
  first-measurement window to Q2 and defers the outcome-tile movement
  by one quarter. The team ships regardless; the product bet gets a
  quarter less in the quarter.

This ask is on Exercise 02's Product-partnership grid as row 6
("AI-adoption budget: features vs enablement"); Decide is manager
within envelope, VP Eng sign-off above threshold — this ask is above
threshold.

### Open loops and next period

**Open loops carried forward.**

- Reviewer-quality decay signal (cognitive-load survey finding) —
  owner: review-standard owner; expected resolution: Q1 instrumentation
  of reviewer-override-rate at P90 band.
- Analytics tile deferred from Q4 swap — owner: PM + Data; expected:
  Q1 week 6.
- Cross-team metric-definition divergence routed to governance
  architect — owner: architect; expected resolution: 2027-02.
- `experimental-mcp-server` trial decision — owner: manager; deadline
  2027-01-15.

**Next period's roadmap at headline level.** `evidence-explain`
scale-out, reviewer-quality instrumentation, analytics tile (shipped
from Q4 swap), one Platform-driven migration. See the roadmap artifact
for details.

**Next period's calendar changes (from Exercise 01 audit).** AI tools
sync → monthly CoP (cadence shift); dashboard review → biweekly (cadence
shift); sprint demo → split into internal + monthly release review
(ritual split, co-signed with PM). Capacity reclaimed: ~399 person-hr/yr.

### Appendices (linked, not duplicated)

- `mod-102` roadmap · `mod-103` review standards + eval set ·
  `mod-104` dashboard · `mod-105` stage ladder · `mod-106` workflow
  catalog · `mod-107` growth plans (team-level signal only) · `mod-108`
  hiring posture · `mod-109` incident retros (×2) · `mod-110`
  governance evidence packet · `mod-111` tool registry.
- Exercise 01 ritual-calendar audit · Exercise 02 decision-rights
  grids · Exercise 04 team one-pager · decision log.
- Previous quarter's pre-read (for ask-outcome review): Q3 2026
  pre-read, in which the ask for CI parallelisation was granted; the
  CI investment is the ~30% platform-investment driver in this
  quarter's productivity section — the compounding evidence that
  previous asks pay rent.

---

### 2.1 Spine choice for this pre-read

Default roadmap-to-outcome arc. The quarter was not dominated by an
incident (two Class C incidents), by a markedly-moved productivity
signal (18% is strong but within a credible band), or by a governance
event. The spine is the roadmap; productivity, governance, vendor, and
people sections thread through it per the Chapter 4 composition pattern.

### 2.2 The three-reviewer discipline (step 12)

Draft sent to three reviewers on 2026-12-15, 48-hour turnaround
requested:

- **PM counterpart (J. Smith).** Roadmap / outcome honesty. Flagged
  one claim ("reviewer-override rate within target band") that needed
  the specific band stated (8–15%); amended.
- **Governance peer (S. Mwangi).** Governance / incident honesty.
  Flagged that the "no Class A or B incidents" line should name the
  Class B near-miss (the eval regression that *blocked* a release).
  Amended to characterise it as the Class C incident it was ultimately
  classified as, with a note that the near-miss is in the gap register.
- **Sister-team peer manager.** Structural honesty — does the one-line
  claim support the detail; does it read to an outsider? Flagged that
  the one-line claim originally read as four claims stacked together.
  Rewrote it tighter.

Review comments returned 2026-12-17. Revisions in. Pre-read finalised
2026-12-18.

### 2.3 Delivery and the VP office hour (steps 13–16)

- **Delivered to direct manager** 2026-12-18, i.e., > 48 business
  hours before the leadership review on 2026-12-22. Confirmed receipt
  logged in the decision log.
- **VP office hour** 2026-12-20 (between delivery and review, 20
  minutes). VP had two questions: the AI-cost over-run driver, and
  whether the Q1 ask was gated on anything else. Both answered; no
  material concessions. Log entry notes the office hour was held.
- **Leadership review** 2026-12-22. One slide (not the document). The
  VP approved the ask as presented, with a note that if `evidence-explain`
  does not show first-measurement movement by Q2 week 6, the 25% is
  reconsidered. Captured as a decision-log entry outcome (reversal
  condition on the ask).

### 2.4 Decision-log entries

A pre-read of this shape produces at least three decision-log entries:

- **DL-2026-12-18-001** — delivery confirmation (short-form; date,
  recipient, version, pre-read link).
- **DL-2026-12-22-002** — leadership-review outcomes: Q1 capacity
  allocation decision (full ADR template; see below).
- **DL-2026-12-10-004** — tool-registry per-workspace spend cap
  (referenced in the governance section; drafted in parallel with the
  pre-read).

The outcomes entry in full:

```markdown
# DL-2026-12-22-002 — Q1 capacity allocation approved at 25% evidence-explain

Date: 2026-12-22
Author: <team manager>
Status: Active
Scope: Team Q1 capacity allocation

## Context

Q4 pre-read ask: approve 25% of Q1 team capacity for `evidence-explain`
scale-out (up from the 15% in Product's initial ask). Trade: 10 pp
from platform-health allocation, discussed with Platform EM (L. Chen)
prior to the pre-read.

## Decision

25% approved for Q1 `evidence-explain` scale-out. 15% remains for
Product-roadmap feature work. Platform-health drops from 20% → 10%
for the quarter.

## Rationale

(1) First-measurement window for `evidence-explain` opens 2026-12-22;
delaying Q2 defers the outcome-tile signal by a quarter. (2) Platform
EM agreed to the 10 pp trade on condition that the Q1 Platform-driven
migration is carried without additional support. (3) The ask routes
to the right Decide role per the Product-partnership grid: manager
within envelope, VP Eng sign-off above threshold (this ask was above
threshold).

## Alternatives considered

- 15% as per Product's initial ask — rejected: too thin to see
  first-measurement movement in Q1; defers outcome signal by one
  quarter; team manager's recommendation was 25%.
- 30% — rejected: Platform EM declined the 15 pp trade; ambient risk
  of platform-health decay above the 10 pp cap.

## Reversal conditions

If `evidence-explain` does not show first-measurement movement by Q2
week 6, the 25% is reconsidered at the next review. If
platform-health signals degrade (incident-recovery-time regresses
> 20%, or CI wall-time regresses > 15%), the 10 pp trade is
rebalanced before Q2 planning.

## Review date

2027-02-28 (mid-Q1 check-in); hard review at Q1 end.

## Links

- Q4 2026 pre-read (this pre-read)
- Exercise 02 Product-partnership grid, row 6
- Platform-EM pre-read agreement (Slack thread, archived)
```

### 2.5 Pre-mortem the ask (stretch applied)

Before shipping the ask, imagined the VP's three likely objections:

- **"Why 25%, not 20%?"** Answered in the pre-read's ask section with
  the first-measurement-window argument.
- **"What does Platform think?"** Pre-addressed by discussing with
  Platform EM before the pre-read; mentioned in the pre-read that the
  trade is agreed.
- **"What is the fallback if the measurement does not move?"**
  Pre-addressed in the reversal conditions of the decision-log entry
  (reconsidered at Q2 week 6).

The VP's two actual questions overlapped the first and third
pre-mortemed objections. The pre-emption saved review minutes.

## 3. Validation steps

Before delivering the pre-read to the direct manager:

1. **Can you write the one-line claim in one sentence?** If not, the
   pre-read does not have a spine; find it and rewrite before
   continuing.
2. **Is there one spine, not four?** Each non-spine section is in
   service of the spine (producing it, protecting it, funding it,
   populating it); if a section stands alone, the composition has
   failed.
3. **Is driver attribution honest?** The productivity section does
   not attribute all gains to AI. Concrete percentage splits with
   source (paired trial, platform investment, roadmap shape, external).
4. **Is the anti-theatre paragraph present and specific?** Not a
   generic "numbers can lie" disclaimer. A specific qualitative-signal
   finding (survey, reviewer behaviour, tile that moved in a
   suspicious direction).
5. **Is the ask one or two items, with decision / deadline /
   consequence?** Three asks is an agenda; prioritise. "No asks this
   quarter" is a legitimate entry.
6. **Did three reviewers read the draft?** PM counterpart, governance
   peer, sister-team peer manager — 48-hour turnaround. Revisions are
   in.
7. **Was the pre-read delivered ≥ 48 business hours before the
   review?** Delivery timing matters; last-minute pre-reads are read
   as scrutiny avoidance.
8. **Was a VP office hour held between delivery and review?** 20
   minutes. Any concessions recorded.
9. **Are outcomes captured in the decision log?** Approvals,
   deferrals, reversal conditions, review dates.
10. **Is the one-pager updated with any material change from the
    review?** The one-pager is the standing executive summary; a
    reviewed quarter's narrative should land on it.

## 4. Rubric

Grade a submitted pre-read + three-reviewer artefacts + decision-log
outcomes. Each criterion 0–3. Pass bar: **22 / 33**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Length and structure** | > 10 pages or < 2 pages | 6–10 pages, structure partial | 4–8 pages, all sections present | 4–8 pages, all sections, appendices linked rather than duplicated |
| R2 | **One-line claim at top** | Missing | Present but multi-claim | One claim, loosely written | One claim, written ruthlessly, the sentence the VP tells their manager |
| R3 | **One spine** | Four parallel reports | Weak spine | Spine identified; one section drifts | One spine; all other sections produce / protect / fund / populate it |
| R4 | **Honest driver attribution** | AI gets 100% credit | AI gets most credit | Four drivers named | Four drivers named with concrete % split + source for each |
| R5 | **Anti-theatre paragraph** | Absent | Generic disclaimer | Specific qualitative finding | Specific finding + planned instrumentation to close the ambiguity |
| R6 | **Governance posture section** | Missing | Numbers without context | Incident counts by class + gap-register delta | Incident counts + gap-register delta + routed-upward state + any policy change |
| R7 | **Vendor / tool-registry section** | Absent | Spend only | Spend + delta + projection | Spend + delta + projection (range) + registry movement + trial-status with deadlines |
| R8 | **The ask** | Three or more asks or ambush decision | Ask without deadline | Ask with deadline but no consequence | One or two asks, each with decision / deadline / consequence; routes to correct Decide role per Exercise 02 |
| R9 | **Three-reviewer discipline** | No reviewers | One reviewer | Two reviewers | PM + governance peer + sister-team peer manager; revisions in; flags recorded |
| R10 | **Delivery timing and VP office hour** | Delivered < 24 hours before review | Delivered day-before | ≥ 48 business hours; office hour not held | ≥ 48 business hours + office hour held + concessions logged |
| R11 | **Outcomes captured in decision log** | None | One-line mention | Decisions logged | Decisions + reversal conditions + review dates logged; links back to the ask |

## 5. Common mistakes

- **No one-line claim.** Without it, the pre-read has no spine and the
  VP invents one. The sentence is the hardest to write; write it last;
  write it ruthlessly.
- **Four parallel reports.** Productivity, governance, vendor, people
  as independent sections with no connective tissue. The VP reads them
  separately and combines incoherently. Pick a spine; make the others
  serve it.
- **AI gets all the productivity credit.** The 18% time-to-ship win
  has more than one driver. Attributing it all to AI builds a story
  that collapses the first time Platform wants credit for the CI
  investment.
- **Generic anti-theatre disclaimer.** "Numbers can mislead" is not
  the anti-theatre paragraph. A specific qualitative finding is —
  reviewer-cognitive-load survey, override-rate at P90, or similar.
- **Ambush asks.** Introducing a decision in the pre-read that has not
  been socialised. Pre-reads formalise decisions already in
  discussion; they do not surprise the VP.
- **No deadline or consequence on the ask.** The VP has no forcing
  function; the decision slides to next quarter. Deadline + consequence
  of inaction is non-negotiable.
- **Personnel actions in the pre-read.** Individual performance,
  hiring decisions belong to 1:1s and the performance cycle. Team-level
  signal only.
- **Attachments pasted inline.** A 25-page pre-read is a structural
  failure. Link the appendices.
- **Board register at L40.** "The firm's AI posture" at L40 is
  signalling, not substance. Use "the team has done X; the team has
  shipped Y."
- **Delivered 2 hours before the review.** Read as scrutiny avoidance.
  48 business hours is the floor.
- **No ask-outcome review of prior quarters.** The compounding credit
  of "last quarter's ask was granted and the outcome materialised"
  does not exist. Carry it forward.
- **No review discipline, or review read as sign-off.** Three
  reviewers for honesty and shape, not for approval. The manager
  owns the pre-read; reviewers do not sign it.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework)
  — the **Govern** function's expectation that an *AI program at
  team-and-up scope has documented roles, cadence, and decision records*
  is why the pre-read is a cadenced artifact delivered to a named
  recipient with a named review ritual. The **Measure** function
  underwrites the driver-attribution discipline (productivity movement
  is a composed signal, not a single-driver claim). The **Manage**
  function's call for *responding to high-priority risks with
  documented actions* is why the governance section names routed-upward
  triggers with their state rather than letting them sit unnoted.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/)
  — the incident-classification posture (prompt-injection as a named
  class rather than a generic "security issue") borrows from OWASP's
  ML-risk taxonomy. The pre-read cites incidents by class because the
  class is the shared vocabulary; "we had two incidents" is not
  actionable, "two Class C prompt-injection attempts, both mitigated"
  is.
- [MITRE ATLAS](https://atlas.mitre.org/) — the governance section's
  gap-register entries use ATLAS-adjacent categories (model-in-the-loop
  attack surface, training data integrity, deployment-chain risk) as
  the shared classification between the team's gap register and the
  governance architect's upstream rollup. Shared vocabulary reduces
  translation cost in the pre-read itself and in the roll-ups that
  consume it.

<!-- follow-up: after two quarters of running the pre-read, add a worked
"ask-outcome review" example at the top of a later pre-read, showing
how the compounding credit of granted-and-delivered asks builds the
credibility of the next ask. The example in this solution carries
forward the Q3 2026 CI-parallelisation ask as the ~30% platform-driver
in Q4; a two-quarter-later example would show a full cycle. -->
