# Exercise 04 — One-Pager and Decision Log Hygiene

> Module: `mod-112-cross-functional-leadership-and-ritual-calendar`
> Exercise type: **design** (worked one-pager, decision-log hygiene protocol, grading rubric)
>
> **Note on exercise prompt.** The learning-side prompt for this
> exercise is scaffolded as TBD. This solution therefore derives the
> exercise shape directly from [Chapter 5 —
> One-Pager and Decision-Log Hygiene](https://github.com/ai-workforce-curriculum/ai-augmented-team-manager-learning)
> (`05-one-pager-and-decision-log-hygiene.md`). When the exercise prompt
> fills in, this solution should be reconciled with any specifics it
> adds; the artifacts below should remain valid because they reflect
> the chapter's named discipline.

## 1. Solution overview

The deliverable is **v0.1 of the team's one-pager plus a hygiene audit
of the decision log**. Both artifacts are what make the operating system
*legible outside the team*: the one-pager is what a new peer, a rotating
skip-level, or a new partner reads in ten minutes to get an accurate
first-approximation mental model; the decision log is what a successor
reads over 30 minutes of recent entries to understand what changed and
why. Together, these two artifacts are what make the manager
replaceable — which is the same thing as making the operating system
real.

The solution encodes four design choices the artifacts will be graded
against:

1. **The one-pager fits on one page when printed** (600–900 words).
   Detail belongs in backing artifacts; the one-pager links to them. A
   one-pager that overflows is a one-pager that no new peer will read.
2. **The one-pager is honest.** Open-loops section non-empty;
   "what-the-team-is-not" section present; any AI-ROI claim is
   qualified with driver attribution. A marketing-voiced one-pager
   teaches peers to read *around* the artifact rather than from it.
3. **The decision log is ADR-shaped and append-only.** Context,
   decision, rationale, alternatives, reversal conditions, review
   date, links — per material decision. Trivial decisions get one-line
   entries; the template is scaled.
4. **The successor-readiness check is binding.** A peer picks the
   team up cold, reads the one-pager in 10 minutes and the last six
   months of log in 30 minutes, and does not need to interview the
   departing manager about anything except judgement calls. If they
   cannot, the artifacts are not successor-ready; the remedy is
   clearer composition, not longer documents.

## 2. Implementation — worked one-pager for "Underwriting-assist team" (fictional calibration)

Rendered as it would appear on the team wiki page. Target: 650–800
words; fits on one printed page.

---

### underwriting-assist · team one-pager

**Version:** v0.1 · **Date:** 2026-11-13 · **Author:** <team manager> ·
**Change log:** `docs/one-pager-changelog.md` · **Canonical URL:**
`wiki/teams/underwriting-assist/one-pager`

**The team in one sentence.** The *underwriting-assist* team owns the
ML-assisted underwriting workflow for consumer credit decisions in the
Americas region, from model-output inference through reviewer handoff
and compliance filing.

**Composition.** 1 manager + 8 ICs. Stage ladder distribution
(`mod-105`): Stage 2 × 1, Stage 3 × 5, Stage 4 × 2. Specialist roles:
tech lead (A. Nguyen), review-standard owner (K. Ramirez), enablement
champ (T. Oyelaran).

**Workflow mix** (`mod-106`). Feature work ~50%; platform maintenance
~20%; review-standard and eval investment ~15%; incident response
~10%; growth / enablement ~5%.

**Measurement anchor.** Dashboard: `dash/underwriting-assist` (`mod-104`).
Three headline tiles currently load-bearing: DX Core 4 time-to-ship,
reviewer-decision-time, reviewer-override rate (target band 8–15%).

**Current roadmap arc.** Q4 2026: `review-assist` v2 (shipped),
`evidence-explain` v1 pilot (shipped, measurement window opens
2026-12-22), platform-health investment. See `roadmap/q4-2026.md`
(`mod-102`).

**Tool and governance posture.**
- **Tool registry** (`mod-111`): 7 keeps, 1 sunset (`competitor-tool`,
  DL-2026-10-22-001), 1 trial (`experimental-mcp-server`, decision
  2027-01-15). Canonical: `registry/underwriting-assist.md`.
- **Governance crosswalk** (`mod-110`): 1 open gap (Article 50
  transparency for Q1 feature), 3 closed in Q4, 1 routed to governance
  architect. Canonical: `governance/crosswalk.md`.
- **Open incident retros**: 0. Last incident: 2026-11-04 (Class C
  eval regression; retro closed; fixture landed).

**Cross-functional partners.**
- **Product:** J. Smith (consumer-credit PM). Grid: `partnerships/product-grid.md`.
- **Platform / DevEx:** L. Chen (shared-platform EM, L50).
- **Design / design-systems:** R. Okafor (fractional).
- **Data / analytics:** M. Patel.
- **AI-Governance peer:** S. Mwangi (`ai-risk-engineer`,
  underwriting cluster).

Decision-rights grids (one per partner) linked from
`partnerships/`.

**Ritual calendar at a glance.** Standup (daily, M–Th); sprint
planning (biweekly); sprint internal demo (biweekly); monthly release
review (joint w/ PM); biweekly dashboard review; monthly CoP (replaces
weekly AI-tools sync, per Exercise 01 audit); monthly eval-set review;
quarterly tool-registry review; semiannual governance retro. Full
calendar: `calendar/underwriting-assist.md`.

**Open loops.**
- Reviewer-quality decay signal (cognitive-load survey). Owner:
  review-standard owner. Expected: Q1 instrumentation of
  reviewer-override rate at P90.
- Analytics tile swapped out of Q4. Owner: PM + Data. Expected: Q1
  week 6.
- Cross-team metric-definition divergence, routed to governance
  architect. Owner: architect. Expected: 2027-02.
- `experimental-mcp-server` trial decision. Owner: manager. Deadline:
  2027-01-15.

**What the team is *not*.** The team does not own the underlying
credit model (that is the Credit-ML team) or the compliance filing
system (that is Risk Operations). We own the assist layer and the
reviewer workflow. We do not own enterprise-level tool procurement
(that is `ai-enablement-lead` L45), we feed into it. We do not own the
organisation's AIMS clause-level evidence (that is `head-of-ai-governance`
L60); we feed into it.

**Honesty note.** DX Core 4 time-to-ship is trending ↓ 18% QoQ with
attribution across four drivers (AI assist ~40%, platform investment
~30%, roadmap composition ~20%, external ~10%); see
`dash/underwriting-assist` for the attribution detail and the
anti-theatre paragraph. "22% faster with AI" would be marketing; the
real number is smaller and the drivers are mixed.

---

### 2.1 Why the sections are in this order

The ordering matches the Chapter 5 shape and the reading pattern a new
peer will follow:

- The single-sentence identity and composition are read in the first
  30 seconds.
- Workflow mix and measurement anchor orient the reader against
  observable team behaviour.
- The roadmap arc puts the team's "right now" above the tool /
  governance posture; a peer reading cold cares about "what is the
  team doing" before "what systems is the team using."
- Partners and the grid link come before the ritual calendar because
  partners are the surface *across* teams; the calendar is the surface
  *inside* the team.
- Open loops and "what the team is not" are the two honesty sections
  — they are above the fold on the page because they do the work of
  earning the rest of the document's credibility.
- The honesty note at the bottom closes the loop: any headline claim
  in the body has an attribution breadcrumb in the footer.

### 2.2 The honesty discipline in practice

Three practices from Chapter 5, applied here:

- **Open-loops section non-empty.** Four open loops listed. Empty
  would signal either an unusual steady-state moment (rare) or a
  one-pager that is hiding things.
- **"What the team is not" present.** Three explicit non-ownerships:
  credit model, compliance filing, enterprise-tool procurement.
  Peers reading this section learn what *not* to escalate here, which
  is more useful than learning what to escalate.
- **No AI-ROI headline without qualifier.** The honesty note at the
  bottom cites the attribution breakdown rather than the headline
  number alone. "22% faster with AI" would be marketing; the 18% with
  four-driver attribution is the real shape.

### 2.3 Canonical-location discipline

The one-pager lives at one URL. If it also appears in a Google Doc, a
Confluence page, and a PDF attached to an email, there are four copies
and no owner. Every link into the team routes to the canonical URL;
peers are told "read this, this is the page." The change log tracks
versions (`v0.1`, `v0.2`, …); peers reading the page always see the
latest.

## 3. Worked decision log — hygiene audit

The decision log at audit time contains ~20 entries from the last year.
The hygiene audit (step 2) walks each entry against the Chapter 5
template and classifies it as **compliant**, **needs amendment**, or
**not-a-log-entry** (should be elsewhere).

### 3.1 Audit snapshot

| # | ID | Title | Status | Hygiene verdict |
|---|---|---|---|---|
| 1 | DL-2026-01-10-001 | Adopt review-standard v2 | Active | compliant |
| 2 | DL-2026-02-03-001 | Migrate eval harness | Superseded by #9 | compliant |
| 3 | DL-2026-02-19-001 | Weekly AI-tools sync (originating decision) | Superseded by #19 | compliant |
| 4 | DL-2026-03-05-002 | Standardise PR template | Active | **amend** — no reversal conditions |
| 5 | DL-2026-04-11-001 | Platform migration opt-in | Active | compliant |
| 6 | DL-2026-05-22-001 | Reviewer calibration cadence | Active | compliant |
| 7 | DL-2026-06-14-001 | IC X promoted to Stage 4 | — | **not-a-log-entry** — individual performance, move to private records |
| 8 | DL-2026-07-18-001 | Q3 roadmap commitment | Superseded by #15 | compliant |
| 9 | DL-2026-08-02-001 | Eval harness migration (revised) | Active | compliant |
| 10 | DL-2026-08-20-001 | Office hours cadence shift | Active | **amend** — review date missing |
| 11 | DL-2026-09-04-001 | Incident-retro routing matrix | Active | compliant |
| 12 | DL-2026-09-15-001 | Governance packet v2 shape | Active | compliant |
| 13 | DL-2026-10-22-001 | Sunset `competitor-tool` | Active | compliant |
| 14 | DL-2026-10-30-002 | PM calibration meeting notes | — | **not-a-log-entry** — meeting notes, move to wiki |
| 15 | DL-2026-10-31-001 | Q4 roadmap commitment | Active | compliant |
| 16 | DL-2026-11-10-001 | Product-partnership grid v0.1 | Active | compliant |
| 17 | DL-2026-11-11-001 | Platform-partnership grid v0.1 | Active | compliant |
| 18 | DL-2026-11-12-001 | Design-partnership grid v0.1 | Active | compliant |
| 19 | DL-2026-11-13-001 | Retire weekly AI-tools sync | Active | compliant |
| 20 | DL-2026-11-13-002 | Dashboard-review cadence shift | Active | compliant |

**Findings.**

- **Two entries should not be in the log.** #7 (individual performance
  — belongs to `mod-108` private records) and #14 (meeting notes —
  belongs on the team wiki). Both are being moved to the correct
  surface; the log entries are marked deleted-and-relocated with a
  pointer to their new homes. The log stays append-only; the relocation
  is itself a log entry (`DL-2026-11-13-003`).
- **Two entries need amendment.** #4 (no reversal conditions) and #10
  (no review date). Both are being amended in place with a dated note.
  Because the log is append-only, the amendment is a new entry
  (`DL-2026-11-13-004` and `DL-2026-11-13-005`) that cross-references
  the original; the original is annotated at the bottom with "amended
  by DL-…" per Chapter 5 practice.
- **No material decisions are missing from the log.** Spot-checked
  against the last six months of material partnership decisions and
  calendar changes; the backfill (stretch goal) is not needed this
  cycle.

### 3.2 Worked amendment entry

The DL-2026-03-05-002 entry originally lacked reversal conditions. The
amendment:

```markdown
# DL-2026-11-13-004 — Amend DL-2026-03-05-002 (standardise PR template): add reversal conditions

Date: 2026-11-13
Author: <team manager>
Status: Active (amendment to DL-2026-03-05-002)
Scope: Team workflow standard

## Context

Hygiene audit of the decision log identified that DL-2026-03-05-002
("Standardise PR template") was recorded without reversal conditions.
Chapter 5 template requires them so the successor knows what would
cause the decision to be revisited.

## Decision

Add the following reversal conditions to DL-2026-03-05-002: (a) if
the PR-review-time tile regresses by >20% over two consecutive months
with no other identified driver; (b) if the template's checklist
items start being routinely skipped (>30% of PRs); (c) if a new
review-standard revision makes the template structurally obsolete.

## Rationale

Original entry recorded the standard but no exit criterion. Successor
cannot tell whether the standard is still load-bearing or should be
amended. Reversal conditions close the loop.

## Alternatives considered

- Edit the original entry in place — rejected: the log is append-only;
  editing originals erodes the artifact's integrity.

## Reversal conditions

If conditions (a)–(c) above are observed, a new decision-log entry
reconsiders the standard.

## Review date

2027-05-13 (semiannual).

## Links

- DL-2026-03-05-002 (original)
- Review-standard v2 charter
- `mod-103` eval set
```

The original entry is annotated at the bottom: `Amended by
DL-2026-11-13-004 on 2026-11-13; reversal conditions added.` The
append-only invariant holds.

### 3.3 Successor-readiness check

Simulated a peer manager picking the team up cold:

- **One-pager read.** Target 10 minutes. The actual read: ~9 minutes
  with a stopwatch; the peer could narrate team identity, workflow
  mix, measurement anchor, roadmap arc, partners, rituals, open
  loops, and scope boundary. Pass.
- **Decision log, last six months.** Target 30 minutes. Entries
  2026-05-22-001 through 2026-11-13-005 ≈ 17 entries at 1–2 minutes
  each = ~25 minutes. The peer could narrate: the ritual-calendar
  refresh, the partnership-grids drafting, the tool sunset, the Q4
  roadmap commitment, the recent governance posture changes. Pass.
- **Residual questions.** The peer had three: (a) why J. Smith
  rather than another PM from the broader PM team — this is
  judgement-call history worth a 10-minute 1:1 with the departing
  manager; (b) what the informal relationship with Platform EM is
  like beyond the grid — same answer; (c) what the growth plans look
  like for the Stage-4 ICs — see individual records (`mod-107`), not
  the one-pager. All three are within Chapter 5's expected
  "handful of judgement-call questions" window. Pass.

## 4. Validation steps

Before publishing v0.1 of the one-pager and closing the decision-log
audit:

1. **Does the one-pager fit on one page when printed?** If not, pull
   detail into backing artifacts and link.
2. **Is the open-loops section non-empty?** If the team is genuinely
   at steady state, say so explicitly with the next-scheduled triggers.
3. **Is "what the team is not" present with 2–4 specific non-ownerships?**
   Generic non-ownerships are weaker than specific ones.
4. **Is every AI-ROI claim qualified?** The honesty note at the bottom
   is non-negotiable if a productivity number appears.
5. **Is the one-pager at one canonical URL?** Multiple copies = no
   owner. Pick a canonical URL; redirect everything to it.
6. **Did every decision-log entry get a hygiene verdict** (compliant /
   amend / not-a-log-entry)? Spot-check at least the last six months.
7. **Were "not-a-log-entry" items relocated** to their correct surfaces
   (private records, meeting notes, etc.), with a pointer in the log?
8. **Were "amend" items amended by new, dated entries** (not by
   in-place edits)? The log is append-only.
9. **Did the successor-readiness check pass?** 10-min one-pager +
   30-min six-month log read + a handful of residual judgement-call
   questions, not many.
10. **Is the one-pager linked from everywhere the team is referenced**
    (team roster, pre-read, partnership grids, governance packet)?

## 5. Rubric

Grade a submitted one-pager + decision-log audit. Each criterion 0–3.
Pass bar: **18 / 27**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **One-pager length** | > 1.5 pages or < 400 words | 1–1.5 pages | One page; 600–900 words | One page; 600–900 words; fits print |
| R2 | **Sections present and ordered** | Multiple missing | Some present | All 11 sections present | All 11 + sensible ordering |
| R3 | **Honesty discipline — open loops** | Empty | One loop | 3–5 loops with owners | 3–5 loops with owners + expected-resolution window |
| R4 | **Honesty — what the team is not** | Absent | Generic | Specific non-ownerships | Specific non-ownerships + routing of who to escalate to instead |
| R5 | **Honesty — AI-ROI qualification** | Headline only | Qualifier present | Qualifier + attribution breakdown | Qualifier + breakdown + link to backing dashboard attribution paragraph |
| R6 | **Canonical-location discipline** | Multiple copies | One copy but not linked | One canonical URL, linked from a few places | One canonical URL, linked from team roster + pre-read + partnership grids + governance packet |
| R7 | **Decision-log hygiene audit ran** | Not run | Partial walk | Full walk; verdicts assigned | Full walk + relocations done + amendments made |
| R8 | **Append-only discipline** | Log edited in place | Some in-place edits | Amendments are new entries | Amendments are new entries + originals annotated with "amended by …" |
| R9 | **Successor-readiness check performed** | Not performed | Reasoned about | Simulated read done | Simulated read + residual-questions list + list is within "handful of judgement-call" window |

## 6. Common mistakes

- **One-pager overflow.** Detail that belongs in backing artifacts
  leaks in. The remedy is not a smaller font; it is harder composition
  and better links.
- **Marketing-voiced one-pager.** Numbers without qualifiers, open-loops
  section empty, "what the team is not" omitted. Peers learn to read
  around the artifact.
- **Multiple copies of the one-pager.** Confluence + Notion + a PDF
  + a slide. Four slightly-different versions = no canonical truth.
- **Individual performance in the decision log.** Belongs to `mod-108`
  private records. The log is team-visible.
- **Meeting notes in the decision log.** Belongs on the team wiki.
  The log is for decisions, not notes.
- **Editing decision-log entries in place.** The log is append-only.
  Amendments are new entries that cross-reference the original.
- **Decision-log entries without reversal conditions or review dates.**
  A decision without a revisit trigger is a decision the org will
  outgrow silently.
- **Treating "no open loops this period" as default.** Steady-state
  moments exist; they are rare. An empty open-loops section earns
  skepticism unless the one-pager explicitly names the trigger that
  will make the next loop visible.
- **Successor-readiness check skipped.** The check is what proves the
  artifacts are not local-to-the-manager. If it is skipped, the
  artifacts have not been stress-tested for the thing they are
  supposed to do.
- **AI-ROI without attribution.** "22% faster with AI" without the
  four-driver breakdown is marketing. Qualify or omit.

## 7. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework)
  — the **Govern** function's calls for *documented policies,
  procedures, roles, and responsibilities* and for *maintaining a record
  of decisions and their rationale* are the structural basis for both
  artifacts. The one-pager documents the team's roles and
  responsibilities; the decision log is the record of decisions and
  rationale. The **Manage** function's call for *reviewing and
  updating* underwrites the review-date discipline on every log entry.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/)
  — the open-loops and "what the team is not" sections protect against
  the governance anti-pattern of implicit-ownership (a risk that OWASP
  flags under governance-adjacent categories). A team that cannot say
  what it does not own cannot route risks to the right owner.
- [MITRE ATLAS](https://atlas.mitre.org/) — the governance-posture
  line on the one-pager, and governance entries in the decision log,
  use ATLAS-adjacent vocabulary where risk categories are referenced
  (prompt injection, training data integrity, model-in-the-loop attack
  surface). Shared vocabulary makes the one-pager usable to the
  governance peer and the architect without translation.

<!-- follow-up: when exercise-04's learning-side prompt fills in, revise
this solution to reconcile with any specifics it adds — particularly
if the prompt names a worked-example team other than the
underwriting-assist calibration used here, or specifies a decision-log
audit cadence other than the one implied by Chapter 5. -->
