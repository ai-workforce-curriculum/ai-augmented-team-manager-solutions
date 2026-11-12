# Exercise 05 — IC Development Plan Template Refresh

> Module: `mod-107-skill-growth-anti-atrophy-and-career-development`
> Exercise type: **design** (worked template + revision rationale + grading rubric)

## 1. Solution overview

The deliverable is a **refreshed IC development plan template** — the artifact
a manager and IC co-author once a quarter to name what the IC will work on
developing, how they will develop it, and what they will produce as evidence.
This exercise refreshes the template for the AI-augmentation era so it
captures the right skills, the right evidence, and the right non-goals.

A good refreshed template is:

- **Short and fillable in one sitting.** If the IC and manager cannot
  co-fill it in a 45-minute meeting, it will not be revisited and it
  will not change behaviour.
- **Centred on 2–3 development goals, not eight.** One quarter, two or
  three targets, each with a concrete artifact. More than three is a
  vibe document.
- **Explicit about the human-only skills.** Defence under adversarial
  review, mentoring, exploration, judgement on security and governance
  risks — the skills that stay the manager's / senior's job even as
  drafting is assisted. If the template does not surface them, the plan
  decays into "learn <new tool> by Friday."
- **Linked to the ladder delta and the research note.** Development
  goals should trace to specific ladder expectations (Exercise 03) and
  specific signals (Exercise 01). The template makes those links
  explicit.
- **Revisable mid-quarter.** The template has an explicit revision
  section for when a goal stops being right. Plans frozen at quarter
  start are plans silently abandoned by week six.

Design decisions this solution encodes:

1. **Development goals are owned by the IC, not the manager.** The
   manager's job is to calibrate, challenge, and allocate capacity.
   The IC's job is to pick the goal and own the artifact. A template
   that structurally lets the manager do the goal-picking produces
   compliance, not growth.
2. **Each goal has an artifact, a venue, and a reviewer.** An artifact
   not reviewed by a named human in a named venue did not happen in
   the way the template cares about. This is the ladder-delta
   discipline flowing into quarterly planning.
3. **The template names an anti-atrophy goal when the signal warrants
   it.** Not every quarter and not every IC; when Exercise 01's
   research surfaced over-reliance or skill erosion, the template
   carries one development goal directly addressing it.
4. **Non-goals are first-class.** The template has an explicit
   non-goals section so the IC is not expected to work on five things
   that are not in the plan. This is what makes "I am not working on
   X right now" a legible decision rather than a performance miss.
5. **AI-tool goals are allowed but not special.** A development goal
   can legitimately be "become fluent in <tool>." That goal gets no
   automatic premium: it is weighed against exploration, defence,
   mentoring, and governance goals by the same rubric. Tools are
   skills on a shelf; they are not the point of the plan.
6. **The template names the reciprocal work.** What will the manager
   do to support each goal? If the plan asks the IC to pair twice a
   week, the manager's row of the plan says "arrange pairing partner
   with IC4-D by <date>." Goals without reciprocal manager work
   decay.

## 2. Implementation — the template

### 2.1 The template, filled

```markdown
# IC Development Plan — <IC name> — Q1 2027

**Author (IC):** <name>
**Manager:** <manager>
**Period:** 2027-01-02 → 2027-03-31
**Links:**
- Ladder addendum v0.1 (mod-107/exercise-03)
- Q4 2026 empirical research note (mod-107/exercise-01)
- Carve-out schedule (mod-107/exercise-02)
**Revision window:** anytime; **mandatory check-in** 2027-02-12 and
2027-03-19 (midpoint and pre-close).

## Development goals (pick 2–3)

### Goal 1 — Rebuild exploration on medium-size changes
**Why this now:** Q4 research surfaced reverts tied to accepted
completions and difficulty explaining diffs in review on 3 of my
medium-sized PRs. Tied to IC3 ladder delta: "can defend the design
of changes produced assistant-first."

**What I will produce this quarter (artifact, venue, reviewer):**
- 3 ADRs, each on a medium-size change I lead, each merged with ≥ 2
  reviewers' comments. Venue: team ADR repo. Reviewer: IC4-D + one
  peer manager via calibration.
- Each ADR names ≥ 2 candidate approaches, not just the one I
  chose.

**How I will develop it:**
- Carve-out block Thu 10:00–12:00, exploration mode (per Exercise
  02). Autocomplete off, chat for lookups only, agent off.
- Pair with IC4-D for the first 2 ADR drafts (90 minutes each).

**What the manager will do:**
- Protect the Thursday block on the calendar (declining conflicts
  through 2027-03-31).
- Set up pairing with IC4-D by 2027-01-10.
- Review each merged ADR within 1 business day.

**How we will know it worked:**
- 3 merged ADRs by 2027-03-31, with reviewer comments showing I
  defended the chosen design against named alternatives.
- In at least one 1:1 this quarter I can walk through one of my own
  PRs end-to-end without opening the assistant.

**How we will know it did not work (early warning):**
- At the 2027-02-12 check-in, no ADR drafted or the drafts do not
  contain named alternatives. Response: convert the goal — see
  revision section — rather than quietly failing it.

### Goal 2 — Build the catching-AI-failure-mode muscle
**Why this now:** Ladder delta names "catches ≥ 1 class of AI
failure mode per quarter in review" for IC3. I have not caught one
in Q4 2026 that I can point to.

**What I will produce this quarter (artifact, venue, reviewer):**
- 1 write-up in `#platform-api-near-misses` of an AI-specific
  failure mode I caught in review this quarter. (If I do not catch
  one, the artifact converts to "one failure-mode analysis of a
  known example from mod-103/exercise-06, applied to our codebase.")
- Reviewer: IC4-D.

**How I will develop it:**
- Add a "I checked for the AI-specific items" note to every PR
  review I do this quarter. Items from mod-103/exercise-01's
  checklist: identifier existence, license, prompt-injection
  surface, licence-poisoned snippet, hallucinated citation.

**What the manager will do:**
- Assign me as the second reviewer on 2 AI-heavy PRs per sprint
  for this quarter.

**How we will know it worked:**
- Write-up merged in the team's near-misses channel by 2027-03-31,
  review comments by IC4-D indicating the catch was substantive.

### Goal 3 — Non-goal: broader design leadership
**Status:** explicit non-goal this quarter.
**Why:** My ladder delta puts broader design leadership at IC4.
Attempting it at IC3 this quarter — on top of Goals 1 and 2 —
would spread me thin. Revisit for Q2 2027 planning.

## Explicit non-goals

- Becoming the team's expert on <new assistant product X>. I am
  comfortable with our current stack; chasing a tool rotation this
  quarter would substitute for the harder goals above.
- Cross-team tech-lead duties. See Goal 3.
- Mentoring IC2s formally. I may answer questions; a scheduled
  mentoring relationship is IC4-D's role this quarter.

## Capacity

- Carve-out block (Exercise 02): 2h / week.
- Pairing hours: 3h over the quarter (2 × 90-minute sessions).
- ADR drafting time: 2–3h per ADR; 3 ADRs; absorbed into sprint
  work, not stacked on top.
- All of the above deducted from sprint capacity in the manager's
  planning doc, not as unpaid overtime. See mod-102 if we are
  overallocated.

## Risk register for the plan itself

- **Risk:** Incident load eats the Thursday block.
  **Mitigation:** two missed blocks triggers reschedule, not log
  (per mod-107/exercise-02's protection policy).
- **Risk:** I drift into "the assistant wrote the ADR" because it is
  faster. **Mitigation:** The ADR's "alternatives considered" section
  has to include one alternative not suggested by the assistant.
- **Risk:** I take the pass on a goal rather than converting it at
  the midpoint. **Mitigation:** Manager and I use the check-in on
  2027-02-12 to make the conversion decision in writing.

## Revision log

- **2027-01-02** — initial plan authored.
- _Future revisions here, with date, who proposed, and what changed._

## Confidentiality

This document is between me and my manager. Portions may be used
anonymised in team-manager calibration discussions (ladder, capacity)
with my consent, named per-use.

## Reviewer signature

- IC: _____________________ (date)
- Manager: _____________________ (date)
```

### 2.2 Why the template looks like this

- **One-sitting rule.** The template fits 2–3 goals, each with five
  short sub-sections. A template that cannot be co-filled in 45
  minutes will not be re-opened at the midpoint and will not shape
  behaviour.
- **Artifact / venue / reviewer triple.** Without all three, a
  development goal is a wish. The triple is the smallest
  specification that makes the goal auditable at the end of the
  quarter.
- **Explicit non-goals.** Managers who cannot name what the IC is
  *not* working on end up with ICs who are implicitly working on
  everything. The non-goals section is where mutual honesty lives.
- **Manager row per goal.** Growth plans that only ask things of the
  IC fail at the midpoint, because the IC is the junior party and
  cannot force protection of their own time. The manager's row
  converts the plan into a two-sided commitment.
- **"How we will know it did not work" is as important as "how we
  will know it worked."** Early-warning criteria are what make
  conversion (changing the goal) a legible decision, not a quiet
  failure.
- **Goal 3 — the explicit non-goal.** A worked example of using a
  numbered slot to *refuse* the extra goal. This is the move that
  gives new managers permission to do the same.
- **Risk register for the plan itself.** The Exercise 02 and Exercise
  01 practice shows up here: the common failure modes for plans like
  this are named and pre-mitigated.
- **Confidentiality clause.** Development plans carry anonymised
  content into calibration; the IC should know and consent, named
  per-use. This is the equivalent of the Exercise 01 consent
  appendix.
- **Signatures.** Not legalistic — just a pair of names and dates.
  A signed plan is a plan both parties have read; an unsigned plan
  is a document the IC emailed back without reading.

## 3. Validation steps

To validate a filled plan before the quarter starts:

1. **Count the goals.** Two or three. Four is "I didn't choose."
   Convert to two.
2. **Walk one goal end-to-end.** From artifact, to venue, to
   reviewer, to what-did-not-work. If any step is unnamed, the goal
   is not ready.
3. **Walk the manager's row for each goal.** If the manager has not
   committed to anything beyond "I will support this," the plan is
   one-sided. Specify.
4. **Check capacity against the sprint ask.** If the plan assumes
   time the sprint does not grant, the plan is unpaid work. Fix the
   sprint ask or cut the goal.
5. **Confirm the links to the ladder delta and Exercise 01 research
   note.** Goals without ladder trace are growth-flavoured busywork;
   goals without research trace are the manager's instinct.
6. **Confirm the review dates are on both calendars.** Midpoint and
   pre-close. Dates only on the manager's calendar are dates that
   will slip.
7. **Read the non-goals section aloud.** If the IC flinches at any
   non-goal, that non-goal is the real conversation. Have it before
   signing.
8. **Signatures and dates present.** No signature, no plan.

## 4. Rubric

Use this to grade a submitted plan (self-assessment or peer review).
Each criterion scores 0–3. Pass bar: **16 / 24**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Goal count discipline** | ≥ 5 goals | 4 goals | 3 goals | 2–3 goals, with explicit non-goal(s) |
| R2 | **Artifact / venue / reviewer per goal** | None | Artifact only | Artifact + venue | Artifact + venue + reviewer, with reviewer on both parties' calendars |
| R3 | **Ladder trace** | None | Vague mention | Each goal cites a ladder expectation | Each goal cites a ladder expectation + the Exercise 01 signal that triggered it |
| R4 | **Anti-atrophy / human-only skill goal** | None | One mentioned | One goal directly targets it | One goal targets it + specifies assistant-off working mode for part of the development |
| R5 | **Manager's row** | None | "I will support" | Each goal has named manager actions | Each goal has named manager actions + named dates + capacity accounted for |
| R6 | **Non-goals section** | Missing | Vague | Specific | Specific + explanation of why each non-goal is deferred |
| R7 | **Risk register and revision policy** | None | Revision log only | Risk register + revision log | Risk register + mitigations + revision log + explicit "convert vs. fail" policy at midpoint |
| R8 | **Consent and confidentiality** | Absent | Mentioned | Clause present | Clause present + named per-use consent for calibration anonymisation + signatures from both parties |

## 5. Common mistakes

- **Five goals.** The manager and IC each added a few; nobody
  cut. The plan is unexecutable; nothing changes.
- **"Become proficient with <tool>" as the headline goal.** Tool
  proficiency is a means, not an end. A plan whose top goal is a
  tool will be stale next quarter when the tool rotates. Keep tool
  goals as sub-tasks in service of a skill goal.
- **No artifact.** "Improve my design skills" is a wish. "Three
  merged ADRs with named alternatives in each" is a goal.
- **Manager row missing.** The plan is one-sided. The IC owns the
  outcome; the manager owns nothing. By mid-quarter the manager has
  not protected the time, and the IC absorbs the failure.
- **Plan frozen at quarter start.** The quarter changes. The plan
  does not. The IC privately decides the plan is a fiction; the
  manager is the last to know.
- **No non-goals.** The IC is implicitly accountable for everything
  else too; growth becomes "shipped more." Under AI augmentation
  this failure mode is especially acute: assistant-amplified
  throughput makes "shipped more" the default framing.
- **No ladder trace.** The plan grows skills the ladder does not
  reward; the IC works hard; at calibration the panel cannot credit
  the work because it was not on the ladder.
- **Consent absent.** The plan's anonymised content is pulled into
  calibration without the IC's knowledge. Trust breaks; future plans
  are performances.
- **Importing the template verbatim.** This template is a worked
  example. The right template for your team names your team's
  artifacts, venues, and ladder references. Localise or the template
  is cosplay.
- **Treating this template as a performance-management tool.** A
  development plan is forward-looking: skills to build, artifacts
  to produce. Performance-management documents are backward-looking
  and have a different format, venue, and HR workflow. Confusing
  the two destroys the development plan's utility.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) —
  the *Govern* function's accountability expectations are the
  structural reason the template names humans (reviewer, manager,
  IC) for every commitment, and the *Measure* function's emphasis
  on observable, documented evidence is the reason each goal
  requires an artifact, venue, and reviewer. The *Manage* function
  frames the risk register for the plan itself as a risk-response
  action set rather than a wellness gesture.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) —
  the "catching AI failure modes" development goal (Goal 2 in the
  worked example) is anchored in the ML / LLM-security categories
  that the ladder delta and review checklist already name. The
  template surfaces this at the plan level so that security
  judgement becomes an explicit development target, not an ambient
  expectation.
- [MITRE ATLAS](https://atlas.mitre.org/) — used here only to
  motivate the breadth of "AI failure mode" categories an IC3+ is
  expected to be able to recognise. The template does not require
  the IC to work every category, only to name the one they worked
  this quarter.

<!-- follow-up: after one full quarter of use, write a short analysis
of which rubric rows were the lowest-scoring across the team's filled
templates. Those are the categories where the template's instructions
need to be sharpened, not where the ICs need more coaching. -->
