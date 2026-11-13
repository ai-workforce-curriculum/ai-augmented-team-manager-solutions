# Exercise 02 — Levelling Criteria Refresh

> Module: `mod-108-hiring-performance-and-feedback-with-ai-in-the-loop`
> Exercise type: **design** (refreshed calibration rubric cards + operator's note + grading rubric)

## 1. Solution overview

The deliverable is a **refreshed set of levelling criteria** that a calibration
committee uses during **performance review season** to decide whether an IC is
*at*, *above*, or *below* their current level under AI augmentation. It is not
a career ladder rewrite (that artifact lives in mod-107 Exercise 03 — *Career
Ladder Under AI Augmentation*), and it is not a hiring rubric (that artifact
lives in this module's Exercise 01 — *Interview Loop Redesign Under AI*). It is
the **rubric cards** the committee chair reads out, one level at a time, while
packets are being discussed.

The artifact is explicitly a **delta** over the team's existing levelling
rubric. A rewrite will not be adopted inside a single cycle, will not survive
cross-manager calibration, and will make the committee argue about structure
instead of people.

A good refresh is:

- **Delta-bounded.** The refresh names, per level, the handful of criteria
  that moved. Everything else inherits from the existing rubric, verbatim.
- **Explicit about downward re-rating.** Behaviour that used to count as
  at-level (e.g., "shipped a well-tested CRUD service with design doc") is
  now called out as **no longer at-level** at some bands, with reasoning.
  Committees that refuse to name this stay anchored on pre-AI output volumes
  and silently over-rate.
- **Evidence-typed.** Each line of the card names the *kind of artifact*
  the IC needs to produce for a reviewer to score them against it — PR
  description, design doc, incident write-up, review comment thread,
  mentoring log. Vague verbs ("drove", "led") are purged.
- **Fair across managers.** The cards are written so that two managers with
  different relationships to AI tools can score the same packet within one
  band. Calibration is the point; manager preference is the enemy.
- **Transparent on appeals.** The refresh names how an IC whose rating
  moves because of the new criteria can see the specific line that changed
  and challenge it. No criteria shift is allowed to land as a surprise in
  a one-way review.
- **Governance-linked.** AI-adjacent incidents (prompt-injection,
  hallucinated API, exposed secret via assistant, model-based decision
  without human sign-off) have a named home in the criteria so they are
  not ambient. This connects into Exercise 04 — *Underperformance
  Root-Cause Drill* when an incident is the trigger.

Design decisions this solution encodes:

1. **Refresh, not rewrite.** The committee needs continuity year-over-year
   so that longitudinal rating trends remain interpretable. A rewrite
   destroys last cycle's data.
2. **Downward re-rating is named in-line, not buried in a preamble.**
   Each level card has a *"no longer counts as at-level"* row. If the
   committee cannot name what moved, the refresh failed.
3. **Evidence weight rises monotonically with level.** IC2 evidence is
   artifact-centric (did the thing get done, is it correct). IC4+ evidence
   is defence-centric (can the IC withstand adversarial review, name the
   failure modes of the assistant that produced it, choose when *not* to
   use one).
4. **Security and governance line up with external frameworks.** NIST AI
   RMF **Govern/Measure**, OWASP ML Top 10, and MITRE ATLAS are referenced
   directly from the criteria rows so the committee has a vocabulary for
   AI-risk performance signals and does not improvise one.
5. **Separation of hiring and performance rubrics is enforced.** The
   refresh carries a header pointer stating "this rubric does not set a
   hiring bar; see Exercise 01 for interview loop criteria." In practice
   committees drift between the two artifacts; this stops that drift
   mechanically.
6. **The operator's note is part of the artifact.** A one-page note for
   the committee chair tells them how to *run* the meeting with these
   cards — which rows to challenge first, how to handle the first case
   that is re-rated downward, when to invoke appeals. Rubric without
   operator instructions regresses to whoever talks loudest.

## 2. Worked implementation — the refreshed calibration cards

### 2.1 Scope and prerequisites

- The team uses an existing IC2–IC5 rubric (IC2 Software Engineer,
  IC3 Senior, IC4 Staff candidate / Staff, IC5 Senior Staff). The refresh
  only touches lines that AI augmentation materially changed.
- At least 50% of the team's shipped code in the review cycle went through
  an AI assistant (Cursor, Copilot, Claude Code, or equivalent). If
  adoption is below that, the manager should run Exercise 01 of mod-107
  first and not land this refresh yet.
- The refresh is **versioned** (`levelling-refresh v0.2 for FY calibration`).
  Committee chairs name the version they used in meeting minutes.
- The refresh goes to HRBP and the manager peer group one week before
  packet freeze. Last-minute refreshes are a documented appeal ground.

### 2.2 The calibration card set

```markdown
# Platform team — performance-review levelling refresh, v0.2
# For: FY calibration committee only. Not a hiring rubric (see Ex. 01).
# Supersedes rows marked in: FY-prior levelling rubric §3.
# Appeals contact: <HRBP + skip manager>; appeal window: 10 business days
#   from rating delivery.

## IC2 — Software Engineer

At-level (must hold ≥ 4 of 5):
- Ships correct, tested changes against defined specs with AI assistance.
  Evidence: PRs with passing CI, description summarising intent, reviewer
  comments resolved.
- Can distinguish when an AI suggestion is wrong on their current task
  (type errors, wrong API, stale import). Evidence: PR comment thread
  showing rejected / corrected suggestions.
- Asks for help within one day on blockers. Evidence: question thread
  with named ask.
- Reads enough of the diff before committing to describe each file's
  change in one sentence. Evidence: PR description quality + spot-check
  in 1:1.
- Owns on-call toil fixes to resolution. Evidence: ticket list + root
  cause note.

No longer counts as at-level (was IC2 at-level in prior cycle):
- "Produced a working first draft of unfamiliar code quickly." This is
  now the assistant's baseline capability and does not demonstrate IC2
  independent judgement.

Above-level signals (promotable toward IC3):
- Catches a class of assistant failure (hallucinated function, wrong
  concurrency pattern) during review of a peer's PR.
- Writes a runbook step after an incident, unprompted.

Below-level signals (concern):
- Merges AI-suggested code they cannot describe when asked in review.
- Repeatedly rediscovers the same bug class the assistant introduces
  (e.g., off-by-one on pagination, swallowed errors).
```

```markdown
## IC3 — Senior Software Engineer

At-level (must hold ≥ 5 of 6):
- Delivers a scoped project (quarter-sized) end to end: design note,
  review of own and peers' PRs, production rollout, follow-up fixes.
- Review quality: on peer PRs, catches at least one substantive issue per
  review on average, with at least one AI-failure-mode catch in the
  cycle. Evidence: review comment export, tagged.
- Design justification: can defend trade-offs in their design doc under
  questioning, *including* rejecting an assistant's suggested design and
  saying why. Evidence: design-review notes.
- Mentors at least one IC2 to above-level on at least one dimension in
  the cycle. Evidence: mentee feedback, specific examples.
- Owns incident response for at least one Sev2+ in cycle: detection to
  write-up to action-item follow-through.
- Spots and raises an AI-usage risk (prompt-injection surface, secret
  leaked into assistant context, unlicensed snippet) at least once and
  drives the remediation. Evidence: ticket or ADR.

No longer counts as at-level (was IC3 at-level in prior cycle):
- "Shipped a medium project with a design doc and passing tests" — this
  is now the IC2 baseline with assistance. IC3 requires defence under
  review, mentoring, and AI-failure-mode catch.
- "Reviewed peer PRs on time" — volume of reviews no longer differentiates;
  depth of catches does.

Above-level signals (promotable toward IC4):
- Named by at least two peers as the person who makes their AI-assisted
  work safer (specific behaviours cited, not vibes).
- Writes a team-level note or ADR that changes how the team uses an
  assistant (context patterns, review rules, eval harness).

Below-level signals (concern):
- Ships volume but every incident post-mortem traces to an assistant
  pattern they did not review.
- Mentoring absent or performative (co-signs IC2 PRs without comments).
```

```markdown
## IC4 — Staff candidate / Staff

At-level (must hold ≥ 5 of 6):
- Owns a system or cross-team initiative. Writes the design doc that
  other teams review. Evidence: ADR + design-review artefacts.
- Design-defence standard: withstands adversarial review from staff peers;
  explicitly argues *when not to use* an AI assistant (e.g., security-
  critical code paths, migrations with destructive ops). Evidence: design
  review meeting notes + decision log.
- Raises the floor: at least one measurable team practice shift in the
  cycle (eval harness, review bar, incident template). Evidence: before /
  after artifact + adoption.
- Review-catch of AI failure modes at a *class* level, not instance level.
  E.g., introduces a lint rule, a review checklist item, or an eval case
  that prevents a recurring assistant error. Evidence: committed rule +
  cases caught.
- Responsible for AI-adjacent governance on their surface: data-flow
  into assistants, model-based decision points, human sign-off boundaries.
  Evidence: documented controls, mapped to NIST AI RMF **Govern/Measure**.
- Grows IC3s: at least one IC3 crosses a dimension toward IC4 under this
  IC's mentorship. Evidence: calibration-committee cross-check with the
  IC3's manager.

No longer counts as at-level (was IC4 at-level in prior cycle):
- "Shipped a system at scale with good uptime" — necessary but not
  sufficient. Must show the design-defence and raise-the-floor rows.
- "Produced many design docs." Document volume does not substitute for
  adversarial-review defence and class-level AI-failure-mode mitigation.

Above-level signals (promotable toward IC5):
- Owns an AI-risk surface at team or org level (eval, governance, incident
  pattern) that peers outside the team cite.

Below-level signals (concern):
- Delegates AI-risk calls to IC3s or skip; does not own the sign-off.
- Design docs look polished but cannot be defended when a staff peer
  from another team challenges them line by line.
```

```markdown
## IC5 — Senior Staff

At-level (must hold ≥ 4 of 5):
- Owns an org-level technical direction and an org-level risk. The
  risk owned explicitly includes at least one AI-adjacent class — model
  governance, eval regime, assistant-usage policy, or an OWASP ML / MITRE
  ATLAS category mapped to the team's surface.
- Writes the thing other staff engineers read and cite — ADRs, eval
  methodology, post-incident patterns. Evidence: external citations
  inside the org.
- Shapes hiring and performance bars (contributes to this refresh, to
  Exercise 01 interview loop, to the career ladder in mod-107 Ex. 03).
  Evidence: committed changes + sign-off trail.
- Decides when the team does **not** adopt an AI practice; names the
  trade-off in writing. Evidence: ADR or org-note.
- Is the person called when an AI-adjacent incident crosses teams.
  Evidence: incident-response involvement + follow-through.

No longer counts as at-level (was IC5 at-level in prior cycle):
- "Produced broad design output and mentored widely." Without named
  governance ownership and named *non-adoption* decisions, this is IC4.

Below-level signals (concern):
- Cheerleads AI adoption without naming the failure modes.
- Absent from the committee artifacts and the incident-response trail.
```

### 2.3 Calibration operator's note (1 page, for committee chair)

```markdown
# Operator's note — FY calibration, levelling refresh v0.2

Before the meeting
- Read every packet once with the prior rubric, once with this refresh.
  Note deltas per IC. If an IC's rating differs between the two passes,
  flag the row that caused it; that is the committee's work.
- Pull the "no longer counts as at-level" rows onto a shared slide. The
  committee will anchor on prior rubric unless forced off it.

In the meeting
- Open the first downward re-rating case explicitly: name the row, name
  the artifact, let the IC's manager defend. Do not let it slide; the
  rest of the committee is watching whether this refresh has teeth.
- For every proposed above-level or below-level call, demand an artifact
  type listed in the card. "I just feel like they're at level" is not a
  sentence this rubric accepts.
- Keep hiring criteria off the board. If someone says "but we hired them
  as a strong IC3," redirect: hiring bar is Exercise 01, not this.
- When an AI-adjacent incident appears in a packet, use the governance
  row on the IC's level card; do not re-litigate incident ownership. If
  attribution is unclear, defer to Exercise 04 root-cause drill — not
  to the committee.

After the meeting
- Every IC whose rating moved because of a refreshed row gets a written
  note citing the row and the artifact. This is the appeal basis.
- Appeal window is 10 business days. Appeals go to HRBP + skip. A
  successful appeal results in a documented rubric clarification for
  next cycle, not a quiet rating change.
- Archive the refresh version with the calibration minutes so next
  cycle can diff.
```

### 2.4 Why these choices and not others

- **Four levels, not the full ladder.** IC1 ratings rarely surface in
  calibration conflict; IC6+ ratings are typically handled by the
  director tier with a different committee. Spending calibration budget
  where the arguments actually happen.
- **"Must hold N of M" instead of all-of-N.** The prior rubric treated
  criteria as a checklist. Under AI augmentation, strong ICs now trade
  axes (e.g., more mentoring, less raw delivery; more review-catch, less
  volume). N-of-M lets the committee honour the trade without
  rewriting the card.
- **Explicit "no longer counts" rows.** Without these, committees drift
  upward in a cycle where assistants raised output volume. This is the
  single most load-bearing feature of the refresh.
- **Framework references inside the criteria rows, not in a footnote.**
  NIST AI RMF and OWASP ML names appear on the IC4 and IC5 cards so
  committee members hear the vocabulary. Burying them in references
  loses them.

## 3. Validation steps

Run all of these before the first calibration session.

1. **Prior-cycle re-score.** Take 6-8 packets from the last cycle and
   re-score them with the refreshed cards. Count how many ratings move.
   Zero movement means the refresh has no teeth; >50% movement means
   it is a rewrite, not a refresh, and will be rejected.
2. **Two-manager read-through.** Give the cards to two managers who did
   not write them and have them score the same packet independently.
   Ratings should land within one band. Record disagreements as rubric
   bugs, not people bugs.
3. **HRBP legal/policy pass.** Share the cards with HRBP and confirm the
   downward re-rating rows do not conflict with the employment handbook
   or jurisdiction-specific rules (especially in the U.S. — see
   Exercise 05 for LL144/EEOC overlap). <!-- tbd-confirm: specific
   jurisdictional review of downward re-rating language varies by
   employer counsel; do not freeze without legal sign-off. -->
4. **Appeals-path dry-run.** Pick one hypothetical IC whose rating would
   move under the new rows. Walk the appeal end to end: who writes the
   note, who receives the appeal, what the remedy is. Fix gaps before
   calibration opens.
5. **Evidence-availability check.** For each card row, confirm the named
   evidence type (PR comment export, review-catch log, incident
   write-up, mentee feedback) actually exists for the current cycle. If
   it does not, strike the row; it cannot be fairly scored.
6. **Hiring-rubric separation test.** Grep the refresh for any line
   that sounds like interview criteria ("can code a tree in 45 minutes",
   "problem-solving under time pressure"). If present, move it to
   Exercise 01 and remove from here.
7. **Governance row trace.** For each AI-adjacent incident in the
   cycle, confirm the IC4 / IC5 governance row would have produced a
   coherent rating. If an incident is unclassifiable, the governance
   language is too vague; tighten it.
8. **Committee-chair walkthrough.** The chair rehearses the operator's
   note aloud with the manager who authored the refresh. If the chair
   cannot defend a row under pushback, the row is not ready.

## 4. Rubric — grading the manager's refresh artifact itself

Score each row 0 / 1 / 2 / 3. Pass bar: **18 / 24**, named *Calibration-Ready*.
Below 14: *Needs rework before cycle*. 14-17: *Workshop with peer managers*.

| # | Dimension | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| 1 | Delta discipline (refresh, not rewrite) | Full rubric rewrite | Large sections rewritten unnecessarily | Delta scoped, a few over-reaches | Clean delta; untouched rows inherit verbatim |
| 2 | Downward re-rating named | Absent | Mentioned in preamble only | One or two rows name it | Every level card names what no longer counts, with reasoning |
| 3 | Evidence specificity | Verbs only ("drove", "led") | Mixed; some artifact types named | Most rows name artifact type | Every row names a specific artifact type the committee can inspect |
| 4 | Cross-manager fairness | One manager's taste | Mild bias; partial calibration | Readable by peers with minor nits | Two independent managers land within one band on test packets |
| 5 | Appeals + transparency | No appeals mechanism | Mentioned, no process | Process named, no owner | Process named with owner, window, and remedy documented |
| 6 | Hiring rubric separation | Hiring language leaks in | Some bleed-through | Mostly separated, one or two overlaps | Explicit header + no hiring lines; Exercise 01 pointer present |
| 7 | AI-risk governance placement | Not mentioned | Mentioned abstractly | One level card names a governance row | IC4 and IC5 cards each have a governance row with evidence and framework reference |
| 8 | Operator's note | Absent | Short preamble only | Partial checklist | Full one-page committee-chair note: before / in / after meeting, with appeals handling |

## 5. Common mistakes

1. **Rewriting the rubric instead of refreshing it.** Cycles get burned
   arguing structure; nobody gets rated.
2. **Omitting the "no longer counts" rows.** Committees anchor on prior
   output expectations and silently over-rate.
3. **Writing criteria as verbs with no artifact.** "Drove impact" cannot
   be scored and will not calibrate across managers.
4. **Conflating hiring bar with performance bar.** IC3 hiring loop
   criteria leak into IC3 performance cards; ICs who are at-performance
   but would not pass a current loop get mis-rated. Keep them separate.
5. **Treating AI adoption as a criterion.** "Uses AI tools heavily"
   describes behaviour, not performance. What matters is defensible
   output and governance.
6. **Letting the governance row be aspirational.** If there is no
   named evidence type for AI-risk ownership, it will be ignored in
   committee.
7. **Shipping the refresh too late for appeals.** Criteria that land
   the week of calibration cannot be fairly applied. Publish early, or
   defer to next cycle.
8. **No versioning.** Next year's committee cannot diff and will argue
   in circles.
9. **Chair runs the meeting without the operator's note.** The rubric
   has teeth only if the chair enforces it; without the note, the
   room drifts to whoever argues hardest.
10. **Using calibration to re-litigate specific incidents.** That is
    Exercise 04's job (underperformance root-cause drill). Calibration
    with the refresh is for *rating against level*, not for
    establishing attribution.

## 6. References

- **NIST AI Risk Management Framework** —
  <https://www.nist.gov/itl/ai-risk-management-framework>.
  The **Govern** and **Measure** functions map directly onto the IC4
  and IC5 governance rows. When the refresh says the staff IC "owns
  AI-adjacent governance on their surface," the specific artifacts the
  committee should expect (documented controls, data-flow, human
  sign-off boundaries, measurement regime) are the NIST AI RMF
  categories. Use the framework as the shared vocabulary so cards
  across managers converge.
- **OWASP Machine Learning Security Top 10** —
  <https://owasp.org/www-project-machine-learning-security-top-10/>.
  Used in the IC3 "AI-usage risk" row and the IC4/IC5 governance rows
  to specify what a *catch* at level looks like. If the IC spotted a
  prompt-injection surface, a data-poisoning vector in a fine-tune, or
  an inference-time leakage pattern, map it to the OWASP ML category
  when citing the evidence. This prevents the committee from accepting
  vague "security awareness" claims.
- **MITRE ATLAS** — <https://atlas.mitre.org/>.
  Used for incident classification on the governance row. When an
  AI-adjacent incident appears in a packet, the committee cites the
  ATLAS tactic/technique it maps to, so that attribution and severity
  are grounded in a public taxonomy rather than one manager's framing.
  This also feeds directly into Exercise 04 — *Underperformance Root-
  Cause Drill* — and Exercise 05 — *LL144/EEOC Compliance Checklist*
  — where the same vocabulary is reused.
