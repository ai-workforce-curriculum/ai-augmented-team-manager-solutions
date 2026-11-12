# Exercise 02 — Deliberate-Practice Carve-Out Schedule

> Module: `mod-107-skill-growth-anti-atrophy-and-career-development`
> Exercise type: **design** (worked schedule + policy rationale + grading rubric)

## 1. Solution overview

The deliverable is a **named, time-boxed, calendar-visible carve-out** in the
team's weekly rhythm during which specific ICs work on a defined skill
*without full assistant reliance*, and the schedule that implements it. This
is the operational lever a team manager pulls when the research in Exercise 01
turns up an atrophy or over-reliance signal they are not willing to let
compound.

A good carve-out schedule is:

- **Small enough to survive the sprint.** If it is 4 hours a week, nobody does
  it. The default is 90–120 minutes a week per IC, with the manager's calendar
  held open as the backstop.
- **Tied to a skill the research actually surfaced.** It is not "general
  learning time." It is "rebuild the exploration step on medium-sized changes"
  or "practise reading other people's SQL end-to-end." Named skills get
  worked; unnamed ones become wasted blocks.
- **Explicit about what assistance is allowed.** Carve-outs that silently
  allow Cursor + agent mode achieve nothing. The policy states which tools
  are on, which are off, and why.
- **Visible on the calendar, protected by the manager, and reported on.** If
  the carve-out never shows on the calendar, it has already lost. The manager
  owns the protection.
- **Reviewed at a named date.** A carve-out that cannot be ended is a
  carve-out that accretes resentment. The exit criteria are part of the
  design.

Design decisions this solution encodes:

1. **Skill target is named per-IC, not per-team.** A team-wide "learning
   Friday" that targets no specific skill produces no specific improvement.
   Each IC on a carve-out has one skill target for the quarter.
2. **The assistant is not demonised.** The carve-out is a scalpel, not a ban.
   Most of the week is still full-assistant; the carve-out is the deliberate
   block where the skill-at-risk is exercised directly.
3. **The carve-out is paid for.** Capacity is explicitly deducted from the
   sprint ask. If the manager does not deduct it, the IC absorbs it as
   evening work; the policy becomes a tax rather than a benefit.
4. **The manager does not grade the carve-out.** Promotion panels do not see
   "what the IC did in deliberate-practice time." Grading the practice
   corrupts it. The manager asks about *the skill downstream*, not the
   practice itself.
5. **The schedule names its own end.** Every carve-out block has a review
   date (default: 90 days). If the signal that triggered it is gone, the
   block is retired. If not, the block is renewed with the new signal.

## 2. Implementation — the schedule

### 2.1 The 90-day carve-out, team "Platform API"

Context: four ICs (IC2 × 2, IC3 × 1, IC4 × 1), running on a two-week sprint.
The Exercise 01 research surfaced: (a) revert-rate uptick tied to accepted
completions, (b) one IC2 unable to defend their own diffs in review, (c) a
single over-reliance escalation.

```markdown
# Platform API deliberate-practice carve-out — Q4 2026

**Owner:** <team manager>
**Window:** 2026-11-15 → 2027-02-14 (90 days)
**Review date:** 2027-02-07 (one week before window closes)
**Capacity deduction:** 2 hours per IC per week, debited from the sprint ask

## Per-IC carve-out blocks

| IC | Weekly block | Skill target | Assistant policy during block | Exit criterion |
|---|---|---|---|---|
| IC2-A | Thu 10:00–12:00 | Rebuild exploration: for the week's largest change, write up three candidate approaches before coding; review approaches in next 1:1. | Autocomplete off. Chat allowed for *looking up docs*. Agent mode off. | 3 consecutive 1:1s where IC2-A explains their own diff under adversarial review without hesitation. |
| IC2-B | Wed 14:00–16:00 | Read-and-explain: picks up two unrelated PRs from the team's history, reads them end-to-end, writes a one-paragraph summary in `#platform-api-reading-club`. | Assistant allowed for lookups; not for writing the summary. | 10 summaries posted; manager has read and left feedback on at least 7. |
| IC3-C | Tue 15:00–17:00 | Design-review muscle: author one ADR per sprint on a decision they were part of; defend it at review. | Full assistant for research; ADR drafted with assistant but final defence is unassisted. | Two ADRs merged with discussion from ≥ 2 reviewers; defence substantive, not formulaic. |
| IC4-D | Fri 13:00–15:00 | Mentoring reps: owns one IC2's exploration output for the quarter; co-signs the exit criterion. Also re-reads one area of the system monthly to keep senior-level map current. | Full assistant for own work; mentoring sessions unassisted. | IC2-A's exit criterion met; IC4-D's own system map updated with one documented surprise per month. |

## Protection policy (manager's job)

1. Blocks are on everyone's calendar as "deliberate practice — do not book
   over." I hold the pen; I decline conflicting invites on the IC's behalf.
2. Sprint planning subtracts 2h × 4 ICs = 8h of capacity before ticket
   assignment. Our velocity is adjusted accordingly for the next three
   sprints.
3. Incident response and customer escalations are the only pre-authorised
   interrupts. "Urgent" feature work from an adjacent team is not an
   interrupt without my written sign-off.
4. Blocks missed to incidents are logged, not rescheduled, unless two
   consecutive blocks are missed for the same IC — then we reschedule.

## What this is not

- It is not a code-free day. It is not a book club. It is not an "offline
  day" — the IC is working in the relevant tools, just with a defined
  constraint on assistance.
- It is not graded. The carve-out never appears on a promo packet; the
  *skills it rebuilds* do.
- It is not a punishment. IC3-C and IC4-D are not on a carve-out because
  they are failing; they are on one because the skills they are practising
  (design review, mentoring) are the ones that remain irreducibly human
  under assistant coverage.

## Review and exit

At the review date, each IC's block either:
- **Retires** — exit criterion met, block removed, next-quarter ladder
  conversation uses the newly-rebuilt skill as evidence.
- **Renews** — exit criterion not met, we write a short note explaining why
  (new signal, old signal persisting, criterion itself was wrong) and run
  for another 45 days.
- **Converts** — the skill-at-risk changed during the quarter; carve-out
  pivots to the new skill with a fresh exit criterion.
```

### 2.2 Why this shape and not the alternatives

- **Per-IC, not per-team.** A "learning Friday" aimed at everyone misses the
  point: not every IC has the same skill-at-risk. Some are over-relying; some
  are not. Some are new; some are senior. Per-IC blocks let the manager match
  the carve-out to the person.
- **90 minutes to 2 hours, weekly, not quarterly off-sites.** Deliberate-
  practice theory (originally from Ericsson's work on expert performance)
  says the dose has to be regular and the task has to be at the edge of the
  practitioner's current ability. Quarterly off-sites satisfy neither
  condition.
- **The IC4 is on a carve-out too.** This signals that carve-outs are not
  remedial. The senior's practice is *mentoring reps and system-map
  freshness*, which is the skill that atrophies at IC4 when they are the
  team's heaviest assistant user. Signalling symmetry prevents the IC2s from
  reading the block as discipline.
- **Assistant policy is per-block, not blanket.** "No AI on Fridays" is a
  policy that the team routes around. "Autocomplete off, chat allowed for
  lookups, agent off, for your Thursday 10–12 block" is a policy that the
  team can execute.
- **Capacity is explicitly paid for.** The single most common failure mode
  is the manager who says "do deliberate practice *and* keep the velocity".
  The IC absorbs this as unpaid overtime, resents it, and the practice
  decays.
- **Grading separation.** If the carve-out were graded directly, the IC
  would optimise for the grade, not the skill. The manager grades the
  skill downstream — in the next design review, in the next promo packet
  — not in the practice itself.

## 3. Validation steps

To validate a carve-out schedule before publishing it to the team:

1. **Does every block cite the Exercise 01 signal it responds to?** If a
   block is in the schedule without a documented signal, delete it — it is
   theatre.
2. **Can you point to the sprint board and show the deducted capacity?** If
   the velocity target was not adjusted, the carve-out is still unpaid work.
3. **Does every exit criterion describe a *behaviour you would see*, not an
   internal state?** "Understands exploration better" is not a criterion.
   "Explains their diff under adversarial review in three consecutive 1:1s"
   is.
4. **Does the schedule name what assistant features are off, and name
   them specifically?** Not "no AI" — "autocomplete off, chat for lookups
   only, agent mode off". If the team cannot execute the policy, the
   schedule does not matter.
5. **Would a sceptical senior IC on the team accept that the carve-out is
   not remedial?** If not, you have not engineered symmetry well enough. The
   IC4's block often does the work here.
6. **Walk the schedule against one sprint's calendar.** Count real interrupts
   (incidents, oncall, customer calls) in the last two sprints. If your
   interrupt budget would have eaten 50%+ of blocks, your schedule is a
   fantasy.
7. **Confirm the review date is on the manager's own calendar.** A review
   date that is not on the manager's calendar does not happen, and the
   carve-out quietly outlives its usefulness.

## 4. Rubric

Use this to grade a submitted schedule (self-assessment or peer review). Each
criterion scores 0–3. Pass bar: **16 / 24**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Signal-to-block traceability** | No signal cited | Signal gestured at | Each block names a signal | Each block names the signal, the artifact, and the date of the research |
| R2 | **Per-IC targeting** | One team block | Mixed | Each IC has a named target | Each IC has a target + the target names the skill at risk, not a topic |
| R3 | **Assistant policy specificity** | "No AI" | Partial feature list | Named features off and allowed | Named features off and allowed, with reason per feature |
| R4 | **Capacity accounting** | Not accounted | Mentioned | Deducted from velocity | Deducted, communicated to skip, reflected in sprint planning doc |
| R5 | **Exit criterion quality** | None | "We'll see how it goes" | Behaviour-based | Behaviour-based + observable in a named venue + measurable |
| R6 | **Symmetry / seniority** | Only juniors carved out | Juniors + one senior | Mix with an IC4/5 block | Mix + senior block targets a skill (mentoring, system-map) that genuinely atrophies at senior level |
| R7 | **Protection mechanics** | None | Named | Named + interrupt budget | Named + interrupt budget + manager declines conflicts in writing |
| R8 | **Review and exit** | No review date | Review date | Review date + possible outcomes | Review date + retire / renew / convert outcomes + ownership of the review |

## 5. Common mistakes

- **Calling it "learning time" with no skill named.** The block decays to
  Twitter and conference videos. Skill-at-risk is *the point*; without it,
  the block is a wellness gesture.
- **Not paying for it.** The manager announces 2h/week of deliberate
  practice and does not adjust the sprint target. The IC works the practice
  on top of the sprint. Resentment compounds and the policy is reversed in
  silence within two cycles.
- **Scheduling the whole team into the same block.** One block for four
  people forecloses the per-IC targeting that makes the exercise work.
- **No assistant-feature specificity.** "No AI Fridays" is unenforceable
  and theatrical. Feature-level policy is enforceable.
- **Grading the carve-out itself.** The moment "what did you do in your
  practice hours?" becomes a promo-packet line, the practice stops being
  practice and starts being performance.
- **Treating the senior as the exception.** If the IC4 is not on a block,
  the juniors read the policy as discipline. Engineer symmetry.
- **No exit criterion.** The block runs forever. The team learns that
  everything the manager does lives forever. Next time, nobody will agree
  to the next block.
- **Importing a template whole from a blog post.** Someone else's
  deliberate-practice schedule is calibrated to someone else's team. Yours
  is a fit to your Exercise 01 signals.
- **Confusing the carve-out with a training programme.** Training (see
  Mod-105) and deliberate practice are different levers. Training is "we
  bring in a workshop on prompt engineering." Deliberate practice is "you
  do the exploration step by hand for 90 minutes on your own change."
  Mixing them is a common confusion; the schedule names only the latter.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) —
  the *Manage* function's framing of **risk response actions** is the
  structural anchor for treating a carve-out as a specific, time-bound,
  measured risk response to a concrete observation from the Measure
  function. The carve-out is not a wellness programme; it is a documented
  action with an exit criterion.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) —
  the ML-security risks remain the manager's responsibility whether or not
  the team is on a carve-out; the schedule should not describe practice
  blocks that would require suspending any applicable security controls.
- [MITRE ATLAS](https://atlas.mitre.org/) — used here only as a reminder
  that over-reliance on assistant output is a known adversarial-exposure
  category in the wider taxonomy; a carve-out that reduces over-reliance
  directly reduces this exposure, which is one reason the practice is
  worth paying for in sprint capacity.

<!-- follow-up: at the 90-day review date, capture the retire/renew/convert
split as a short write-up and attach it next to Exercise 01's quarterly
research note. That is the feedback loop that makes the carve-out a
learning policy instead of a one-shot. -->
