# Exercise 06 — Headcount Justification Exec Pre-Read

> Module: mod-108-hiring-performance-and-feedback-with-ai-in-the-loop
> Exercise type: **design**

## 1. Solution overview

The deliverable is a 1–2 page pre-read document a line manager sends to a skip-level or exec staff
audience ahead of a headcount decision meeting. It is written in the uncomfortable climate where
leadership has internalized "AI makes engineers faster" and will, by default, reject the ask with
"scale the team you have with more tooling." The pre-read's job is to make the decision easier and
faster, not to lobby for a yes.

Good looks like:

- A reader who has never met the team can finish it in under five minutes and arrive at the same
  decision the author would.
- The AI counter-case ("why not scale with tooling?") is addressed on page one, not buried.
- A `no-headcount` outcome is a valid success state. The document is calibrated, not pitched.
- Non-amplifiable work is enumerated by concrete artifact, not described as "senior judgment."
- Alternatives are explicitly listed and rejected with reasons the reader could contest.
- The decision has a named owner, a by-when date, and a stated form ("approve N SWE-3 reqs by
  Q-end, posted within two weeks").

Six design decisions baked into the format:

1. **TL;DR comes first and admits the uncomfortable framing.** The first sentence is not "we had a
   great quarter" — it is "we are asking for N heads despite AI-measured throughput gains of X%."
   Burying the tension costs the reader's trust in the first thirty seconds.
2. **AI-saturation analysis is a required section, not an appendix.** The pre-read states, for the
   current team's work mix, roughly what fraction was amplifiable and what fraction was not. If the
   author cannot answer this, the ask is not ready. Cross-reference mod-104 for how the measurement
   was done.
3. **Bottleneck work is named by artifact and role, not by adjective.** "Incident command for
   paging tier-1 services," "architecture review of cross-team RFCs," "design calibration with
   partner PMs" — not "senior work" or "hard problems."
4. **Alternatives are considered and *rejected on the page*.** Contractors, cross-team loans, scope
   cuts, and deeper automation each get one line and one reason. Omitting them invites the reader
   to raise them in the meeting and consume the time slot.
5. **Risks are governed items with owners and review dates (NIST AI RMF framing).** Attrition
   exposure, key-person risk, hiring-market risk, and AI-assistant-regression risk each have a
   named owner and a review cadence, not a rhetorical shrug.
6. **Decision ask is binary, time-boxed, and signed.** "Approve N reqs of level L, posted by
   date D, close by date D+X, named decision-maker M." Without those four fields the pre-read is
   a status update, not a decision document.

The pre-read is distinct from the vendor-spend ask (mod-111) and the ROI write-up (mod-104). Those
documents answer different questions with different stakeholders; mixing them produces a document
that answers none of the three well.

## 2. Worked implementation — the pre-read

### 2.1 Scope and prerequisites

Before writing, the author needs:

- Team's trailing 2 quarters of throughput data, segmented by AI-amplifiable vs. not
  (mod-104 productivity measurement).
- Team's current OKRs and the scope at risk (exercise 03 in this module for the career-ladder
  context; mod-104 for the delivery context).
- Current org chart and the exec's prior stated position on AI-driven efficiency.
- A short list of alternatives the author has *actually* evaluated, not strawmen.
- The name of the person who signs the req, and that person's calendar for the next two weeks.

The pre-read is not the forum to debate measurement methodology. If the measurement is contested,
that is a separate meeting; link to the mod-104 artifact and move on.

### 2.2 Pre-read template

```markdown
# Headcount pre-read — Team <TEAM>, <QUARTER>

**Author:** <manager>  **Skip:** <skip>  **Decision-maker:** <exec name>
**Decision needed by:** <YYYY-MM-DD>  **Decision form:** approve / defer / decline N reqs
**Related docs:** <link to mod-104 ROI write-up> · <link to team OKRs>

## TL;DR
We are asking for **<N> engineers at level <L>, posted by <date>, close by <date>**, despite a
measured **<X>%** throughput gain from AI assistants over the last two quarters. The gain is real
but concentrated in <code-authoring / test scaffolding / doc generation>; the backlog growing
fastest is in <incident load / cross-team design review / platform migrations>, which is not
AI-amplifiable at this time. Declining the ask will slip <named commitment> by <N> weeks and
expose <named risk>; approving it recovers that slip and reduces key-person risk on <service>.

## 1. The ask
- **Count:** <N> engineers.
- **Level:** <L> (e.g., SWE-3), with <M> at <L+1> acceptable in trade.
- **Skills:** <distributed systems / on-call eligible / embedded Python / ML infra — pick 2–3,
  not a wishlist>.
- **Start window:** posted by <date>, offers out by <date+6w>, start by <date+12w>.
- **Allocation:** <service / workstream> — not fungible across the org.
- **Budget source:** <already in plan / needs trade against X / needs new allocation>.

## 2. "Why not just scale with more AI?"
This is the correct first question. Short answer:

- **AI-amplified share of current throughput:** ~<X>% of merged diffs and ~<Y>% of docs/tests,
  measured <how, link to mod-104>. This is near the ceiling for the current work mix; further
  tooling investment in this surface has diminishing marginal return <!-- tbd-confirm: cite
  internal measurement, not external study -->.
- **Non-amplifiable share and why:** ~<Z>% of engineer-hours go to work AI assistants currently
  cannot do unattended at our risk bar — enumerated in §4.
- **Tooling investments already made this half:** <list 2–3 concrete investments and the
  measured lift from each>. We are not under-invested in tooling; we are under-invested in the
  humans the tooling multiplies.
- **Failure mode if we substitute tooling for heads:** <specific: on-call rotation collapses to
  3 people / senior reviewer becomes single point of failure / mentoring load for new grads
  falls off entirely>.

## 3. AI-saturation analysis (current state)
| Work category                      | % of team hrs | AI-amplified today | Headroom |
|-----------------------------------|---------------|--------------------|----------|
| Feature code authoring             | ~<a>%         | High               | Low      |
| Test authoring and scaffolding     | ~<b>%         | High               | Low      |
| Code review (peer and senior)      | ~<c>%         | Partial            | Medium   |
| Incident response and on-call      | ~<d>%         | Low                | Low      |
| Cross-team design review / RFCs    | ~<e>%         | Low                | Low      |
| Stakeholder / PM coordination      | ~<f>%         | None               | None     |
| Mentoring and calibration          | ~<g>%         | None               | None     |

Methodology and raw data: <link to mod-104 artifact>. Caveats: <sampling window, measurement
error bar>.

## 4. Bottleneck work — named, not adjectival
The following work items blocked delivery last quarter and are not AI-amplifiable at our risk
tolerance:

1. **Incident command for <service tier-1>.** Requires on-call eligibility, production access,
   human judgment under ambiguity. Current rotation: <N> people. Target: <N+2>.
2. **Senior code review on cross-team changes to <platform>.** Reviewer-of-record must own the
   call on API compatibility. AI pre-review reduces author iteration time but does not reduce
   the reviewer-of-record load.
3. **Design calibration with <partner team / PM org>.** Requires continuity across weeks of
   context, trust-building, and the authority to say no. Non-delegable to assistants.
4. **Mentoring the <N> engineers hired in <prior cycle>.** Current ratio is <X>:1 and dropping
   quality signal (ref: calibration notes from exercise 03).
5. **Migration of <legacy system> off <dependency>.** Requires reading poorly-documented
   production behavior and negotiating cutover with <N> downstream teams.

## 5. Opportunity cost if the ask is declined
In outcome terms, not activity terms:

- **<Named commitment A>** slips by ~<N> weeks. Downstream effect: <customer / partner team>.
- **<Named commitment B>** drops from the half. We will communicate this to <stakeholder> by
  <date>.
- **On-call load per engineer** rises from <X> to <Y> weeks/year. Historical attrition
  correlation: <!-- tbd-confirm: cite internal attrition data or omit -->.
- **Mentoring load** falls below our calibrated minimum from exercise 03; expect ladder-stall
  risk for <N> junior engineers.

Not in scope here: generic "we'll ship less." If a line can't be made concrete, it is cut.

## 6. Risks and dependencies (governed)
| Risk                              | Owner       | Review date | Mitigation                     |
|-----------------------------------|-------------|-------------|--------------------------------|
| Hiring market tightens at level L | <manager>   | <date>      | Widen to L±1; open remote      |
| Key-person risk on <service>      | <tech lead> | <date>      | Fund <N>th on-call seat first  |
| AI-assistant regression / outage  | <manager>   | <date>      | Fallback runbook in <link>     |
| Attrition of senior IC            | <skip>      | <date>      | Retention conversation by <d>  |
| Scope grows before heads arrive   | <PM>        | <date>      | Scope lock by <date>           |

Framed per NIST AI RMF: each risk has a named owner, a review date, and a stated mitigation.
Items without all three are cut from this table.

## 7. Alternatives considered and rejected
- **Contractor / staff aug.** Rejected: on-call eligibility and production access bar excludes
  short-term contractors for the roles that are actually bottlenecked (§4 items 1, 2, 5).
  Viable for <specific non-bottleneck work>, which is not where the backlog is.
- **Cross-team loan from <team>.** Rejected: <team> is itself under-staffed per <their pre-read
  / their skip>; borrowing would transfer the slip, not remove it.
- **Scope cut.** Partially accepted: already cutting <workstream>. Further cut below the line
  removes <commitment> that <stakeholder> has already sequenced against.
- **Deeper automation / tooling.** Rejected as a substitute: §2 shows tooling ROI is
  diminishing on current surface. Accepted as a complement: <specific tooling investment>
  is still funded this half.
- **Do nothing and reset expectations.** Rejected: §5 opportunity cost exceeds the fully-loaded
  cost of the asked heads for the next four quarters.

## 8. What a successful decision looks like
- **Approve:** <N> reqs at <L> posted by <date>, closed by <date+12w>. Re-forecast delivery at
  <date+4w> once pipeline is visible.
- **Partial approve:** <N-1> reqs. We take the <§5 line item> hit on <commitment B> and
  communicate to <stakeholder> by <date>.
- **Decline:** we scope-cut <workstream> by <date>, formally transfer <service> on-call to
  <team>, and reset the half's commit with <skip> by <date>. This is a valid outcome; the
  document is calibrated for it.

**Decision needed by:** <YYYY-MM-DD>. **Decision-maker:** <name>. **If no decision by that
date, the default is decline and we execute §8 "Decline" path.**
```

### 2.3 How to write your own — authoring heuristics

- **Draft the TL;DR last, but print it first.** If you cannot compress to three sentences, you
  do not yet understand your own ask.
- **Pre-mortem the exec's objection in writing.** Literally write "if the exec says 'just use
  AI,' my one-paragraph answer is …" before you write anything else. If the paragraph is weak,
  the ask is weak.
- **Measure before you ask.** The AI-saturation table is the load-bearing section. If the
  numbers are vibes, the reader will know. Point at mod-104.
- **Name people and artifacts.** "Incident command for payments-api tier-1" beats "senior
  operational work" every time.
- **Kill every sentence that would still be true if you were not asking for heads.** Generic
  team-is-great prose dilutes the signal. Cut it.
- **Write the "decline" branch honestly.** If §8 "Decline" is a strawman, the whole document
  reads as a pitch. If it is a real plan, the reader trusts the "approve" branch.
- **Keep it to two pages.** Appendices (raw data, methodology, prior pre-reads) are fine.
  Front matter is not.
- **Circulate 48 hours before the meeting.** Pre-reads read in the room are status updates.

## 3. Validation steps

Before sending, the author runs these checks. Any failure sends the draft back to revision, not
to the exec.

1. **Three-sentence TL;DR test.** Cover the rest of the doc and read only the TL;DR. Does it
   state the ask, the AI-gain context, and the consequence? If not, rewrite.
2. **Exec counter-case test.** Read §2 alone. Would the exec's "just scale with AI" objection
   be answered? Would a skeptic contest it? If the answer is "no" to either, the section is not
   doing its job.
3. **Named-artifact test on §4.** For each bottleneck, point at a specific service, review, or
   artifact. If any item reduces to an adjective ("senior," "hard," "complex"), rewrite it.
4. **Alternatives-are-real test.** For each rejected alternative in §7, could you name the
   person you talked to or the analysis you did? If an alternative is a strawman, remove it
   or do the work.
5. **Decline-path plausibility test.** Hand §8 "Decline" to a peer manager. Would they accept
   it as an executable plan? If they laugh, the branch is a prop and the document is a pitch.
6. **Numbers-cite-mod-104 test.** Every percentage in §2 and §3 links to an underlying
   measurement artifact. No bare numbers.
7. **Decision-owner-and-date test.** The exact name of the decision-maker and the exact date
   appear in the TL;DR and §8. "Leadership" and "soon" are rejected.
8. **Length test.** The main body (excluding appendices) prints on 1–2 pages at the exec's
   usual font size. If it is longer, you are negotiating in writing; stop.

## 4. Rubric

Peers review one another's pre-reads using this rubric. Pass bar: **18 / 24**. A document that
scores below pass goes back to the author before it reaches the exec.

| # | Criterion                                                             | 0                              | 1                                | 2                                                  | 3                                                                        |
|---|-----------------------------------------------------------------------|--------------------------------|----------------------------------|----------------------------------------------------|--------------------------------------------------------------------------|
| 1 | TL;DR states ask, AI context, and consequence in <= 3 sentences       | Missing or prose-y             | States ask only                  | States ask + one of AI context / consequence       | States all three, 3 sentences, specific                                  |
| 2 | Exec counter-case ("just use AI") addressed up front                  | Not addressed                  | Addressed only in appendix       | Addressed but defensive / hand-wavy                | Addressed first, with saturation data and named failure mode             |
| 3 | AI-saturation analysis (§3) is measured, not asserted                 | No numbers                     | Numbers without source           | Numbers with weak source                           | Numbers with link to mod-104 artifact and error bar                      |
| 4 | Bottleneck work (§4) named by concrete artifact                       | Adjectives only                | Some concrete, some generic      | Mostly concrete                                    | Every item names service / review / stakeholder                          |
| 5 | Opportunity cost (§5) in outcome terms, not activity terms            | "We'll ship less"              | One concrete, rest vague         | Most concrete                                      | Every line names a commitment, stakeholder, and slip size                |
| 6 | Risks (§6) are governed items with owner + review date                | No table                       | Table, missing owners or dates   | Table complete, mitigations vague                  | Every row has owner, date, concrete mitigation (NIST AI RMF framing)     |
| 7 | Alternatives (§7) each rejected with a contestable reason             | Not listed                     | Listed, strawman reasons         | Listed, some real analysis                        | Each alternative has evidence / name / document behind the rejection      |
| 8 | Decision ask (§8) has count, level, date, named decision-maker        | Missing two or more fields     | Missing one field                | All fields present but decline path weak           | All fields present and decline path is a real executable plan            |

## 5. Common mistakes

1. **Leading with team accomplishments.** The reader is budgeting time, not applause. Save
   accomplishments for the end-of-half review; this document is a decision input.
2. **Burying the AI counter-case on page three.** If "why not AI" appears after the ask, the
   exec will interrupt and the meeting will relitigate §2 instead of deciding.
3. **"Senior judgment" as the bottleneck.** This is not a reason; it is a label. Name the
   artifact, the service, or the stakeholder relationship that actually blocks.
4. **Strawman alternatives.** Listing "hire 20 contractors" as the rejected alternative when
   nobody suggested it. The reader will notice and discount the rest.
5. **Opportunity cost stated as effort, not outcome.** "The team will be overloaded" is not a
   consequence the exec can decide against. "<Partner team>'s migration slips by 6 weeks" is.
6. **Risks as rhetorical devices.** "There is a risk of attrition" with no owner, no date, and
   no mitigation is noise. Govern it (NIST AI RMF) or cut it.
7. **No named decision-maker.** "Leadership to decide" means nobody is on the hook for the
   date. Pre-reads without a named owner stall by default.
8. **Conflating this doc with the vendor-spend ask.** Tooling budget belongs in mod-111's
   artifact with a different reviewer. Mixing them makes both weaker.
9. **Conflating this doc with the ROI write-up.** mod-104 proves the AI gains are real; this
   doc assumes that proof and moves on. If the reader is still litigating measurement, send
   them to mod-104 and reschedule.
10. **Treating "decline" as failure.** If the author cannot write a credible decline branch,
    the exec will infer that the document is lobbying, not deciding, and will discount it.

## 6. References

- **NIST AI Risk Management Framework** — <https://www.nist.gov/itl/ai-risk-management-framework>.
  Relevance: the Govern / Map / Measure / Manage functions are the structural basis for §6
  (risks as named, owned, dated items with mitigations). The pre-read's claim that AI-amplified
  throughput is bounded by non-amplifiable work is itself a Measure-function statement; its
  risks table is a Manage-function statement. If the exec challenges the risk framing, point at
  this framework rather than defending ad hoc.
- **OWASP Machine Learning Security Top 10** —
  <https://owasp.org/www-project-machine-learning-security-top-10/>. Relevance: when the §6
  risk "AI-assistant regression / outage" is challenged ("why is that in a headcount doc?"),
  cite OWASP ML categories such as model-skewing and supply-chain risks for the AI assistants
  the team depends on. The governed-risk framing is defensible because those risks are
  catalogued, not invented.
- **MITRE ATLAS** — <https://atlas.mitre.org/>. Relevance: ATLAS's adversary-TTP catalog
  underpins the argument that AI-assistant reliability is a dependency risk, not a stable
  assumption. If the exec's counter-case is "AI will keep making the team faster indefinitely,"
  ATLAS gives the vocabulary for why the dependency itself must be governed — which is why
  substituting tooling for humans is bounded by risk tolerance, not just by productivity.

Cross-references inside this curriculum:

- **Exercise 03 (career ladder under AI augmentation)** — the mentoring and ladder-calibration
  load referenced in §4 and §5 is defined there. Without that context, "mentoring load" reads
  as sentiment; with it, it reads as a calibrated throughput constraint.
- **mod-104 (productivity measurement and AI ROI)** — the AI-amplified throughput percentages
  in §2 and §3 must point at a mod-104 artifact. This document does not re-derive them.
- **mod-111 (vendor and tooling spend governance)** — distinct venue, distinct audience, do
  not conflate. The "deeper automation" alternative in §7 may trigger a mod-111 ask, but it
  does not live in this document.
