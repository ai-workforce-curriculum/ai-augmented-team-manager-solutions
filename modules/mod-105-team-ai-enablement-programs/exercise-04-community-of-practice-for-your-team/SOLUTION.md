# Exercise 04 — A Community of Practice Charter for Your Team

> Module: `mod-105-team-ai-enablement-programs`
> Exercise type: **design** (worked charter + decision rationale + grading rubric)

## 1. Solution overview

A Community of Practice (CoP) in this context is a cross-team or intra-team group whose purpose is to accelerate *shared learning* about AI-assisted work — tools, prompts, failure modes, review patterns, and policy. Done well, a CoP absorbs the common lessons once and feeds every team. Done poorly, it is a Slack channel that nobody posts in, or a monthly meeting that ran twice and was quietly de-listed.

The deliverable is a **charter**: a short written contract that answers who, what, why, when, and how. Charter-less CoPs die; chartered CoPs survive the first attrition and the first calendar crunch.

The charter has seven sections:

1. **Purpose and non-purpose** (what this group is *for* and, crucially, what it is not).
2. **Scope boundaries** (which practices fall inside, which belong to review / governance / hiring).
3. **Membership** (who is in, how new members join, how members leave).
4. **Rituals and cadence** (the minimum viable set of recurring meetings and asynchronous practices).
5. **Deliverables and ownership** (what the CoP produces, who owns each artifact).
6. **Rules of engagement** (confidentiality, disclosure, policy escalation).
7. **Sunset criteria** (when this CoP should be dissolved or absorbed).

Design decisions this solution encodes:

1. **Purpose and non-purpose are *paired*.** Every CoP charter that survives six months names its non-purposes explicitly. "This CoP does not set policy" / "This CoP does not grade individual performance" / "This CoP does not approve tool procurement" — these negations are what prevent scope drift into the territory of leadership, review, or HR.
2. **Minimum-viable ritual set.** A CoP needs *at most* two recurring rituals: one synchronous (90-minute monthly working session or a 30-minute biweekly demo), and one asynchronous (a shared backlog / wiki / channel with a weekly curation turn). Three or more recurring rituals is the fastest way to kill a volunteer community.
3. **Membership is bounded but open.** A CoP of 40 is a mailing list. A CoP of 6–10 is a working group. The charter specifies a target range and a joining protocol so growth doesn't dilute the room.
4. **Deliverables are *artifact-forward*.** Meeting notes are not a deliverable. A pattern library, a prompt library, a shared evals set, an incident digest, a monthly brief — these are deliverables. If the CoP produces nothing you can link to, it is not a CoP, it is a chat.
5. **Explicit escalation path.** When the CoP identifies a decision only the org can make — a policy change, a tool procurement, an incident — the charter names where that decision goes. Without that, the CoP becomes a complaint-collection point.
6. **Sunset by default.** The charter includes dissolution criteria. CoPs that cannot dissolve become zombie meetings.
7. **Alignment with governance.** The CoP is a *practice* body, not a *policy* body. Policy belongs upstream (mod-110 governance). The charter makes that boundary explicit so the CoP doesn't quietly accumulate policy authority it was never granted.

## 2. Worked implementation — the charter

### 2.1 Charter template

Save as `ai-cop-charter-v1.md` in the shared wiki. First version below is annotated; the un-annotated one-pager follows in §2.2.

```markdown
# AI Practices Community of Practice — Charter

Version: 1.0 · Owners: <manager A>, <manager B> · Review cadence:
  quarterly · Next review: <date>

## 1. Purpose
We are a working group of engineers and engineering managers who
share what we are learning about AI-assisted work — prompts, review
patterns, failure modes, and tooling choices — so that each team
does not have to re-learn the same lessons in isolation.

## 2. Non-purposes
We are NOT:
- A policy-setting body. Policy belongs to the AI governance
  owner(s) at org scope. We may draft proposals; we do not
  ratify them.
- A procurement committee. We may surface tool needs and gaps; we do
  not approve or reject tools.
- A performance-review venue. Individual IC fluency is a manager
  concern, not a CoP concern. We never discuss named individuals'
  scores, reviews, or compensation.
- An incident-response team. We may digest incident lessons AFTER
  the responders close them; we do not triage.
- A mandatory training. Membership is voluntary; attendance is not
  a performance signal.

## 3. Scope boundaries
Inside scope:
- Prompt and workflow patterns.
- Review-checklist evolution (see mod-103 ex-01).
- Shared evals and judge-prompt sharing (see mod-103 ex-05).
- Failure-mode taxonomies (what the assistant got wrong this month).
- Tool-selection heuristics at IC scale.
- Fluency-growth artifacts (see Exercise 03).
Outside scope — belongs to other bodies:
- AI policy and acceptable-use → mod-110 governance owner.
- Incident response → on-call + accountability contract (mod-109).
- Hiring and performance → mod-108.
- Vendor budget and procurement → mod-111.
- Workflow redesign at the system level → mod-106 (practice
  experiments CAN be shared here; organisation-wide redesign goes
  through the design-review path).

## 4. Membership

Target size: 6–10 members.
Representation target: at least one engineer and one manager from
  each participating team; at least one practitioner from
  security / privacy / data if the org has those functions.

Joining:
- An interested person reads this charter.
- They post an intro in the async channel naming one practice they
  want to contribute and one they want to learn.
- They are added by a steward within one week.

Leaving:
- Members may leave at any time. Preferred form: a one-line note so
  stewards can keep the list current.
- Members who miss three consecutive syncs without posting
  asynchronously are moved to an "alumni" list; they rejoin with a
  one-line note.

Stewardship:
- Two stewards (one engineer, one manager) rotate every two
  quarters. Stewards curate the async feed and run the sync ritual.

## 5. Rituals

### 5.1 Synchronous: monthly working session (90 minutes)
Agenda (fixed shape, filled 48h in advance by stewards):
- 10 min: last month's deliverables and one surprise.
- 20 min: demo slot — one member walks through a recent assisted
  artifact and the failure modes they caught.
- 30 min: pattern work — the group drafts or revises one entry in
  the pattern library.
- 20 min: open floor and escalations to governance / vendor / other
  bodies.
- 10 min: next month's deliverable owners.

### 5.2 Asynchronous: shared channel + weekly curation
- Channel for ongoing discussion, prompts, and artefacts.
- Every Friday, the on-rotation steward posts a 3-bullet summary of
  the week's most useful threads. If nothing is worth summarising,
  the steward says so explicitly — silent weeks are allowed.

### 5.3 (Optional) Quarterly write-up
- A one-page brief summarising what the CoP produced that quarter,
  shared with each participating team's manager and any org-scope
  enablement lead (see Exercise 05).

## 6. Deliverables

The CoP's artifacts are visible, linked, and owned:

| Deliverable | Owner | Cadence |
|---|---|---|
| Pattern library (prompt + workflow patterns) | Steward-eng | Rolling; one new or revised entry per sync |
| Failure-mode digest | Steward-mgr | Monthly, derived from member journals |
| Shared evals / judge-prompt library | Volunteer per entry | Added as members contribute |
| Review-checklist proposals | Steward-eng | Quarterly; proposals go upstream to review-standards owner |
| Quarterly brief (optional) | Stewards | Quarterly |

Members' own journals (see Exercise 03 artifact B) are personal —
never copied into CoP deliverables without consent.

## 7. Rules of engagement

- **Disclosure.** When sharing an artifact in the CoP, disclose the
  tool and mode used, per the team's acceptable-use policy.
- **PII and confidentiality.** Never paste internal customer data,
  customer identifiers, security tokens, or named-person performance
  commentary into the CoP channel or deliverables. Redact first.
- **No named-individual performance discussion.** Discuss artifacts
  by their technical content. "On my team, an IC produced an
  assisted PR where …" is fine; the IC's name is not.
- **Escalation.** Items the CoP cannot resolve go to the
  appropriate owner: policy → governance lead; vendor → vendor
  owner; incident → on-call. The CoP never retains decision
  authority on these.
- **Openness.** Deliverables are internal-public (visible to any
  employee) unless they contain sensitive material. Draft status is
  labelled.

## 8. Sunset criteria

This CoP dissolves (or is absorbed) if any of the following holds
for two consecutive quarters:

- Fewer than three members attending sync rituals.
- Zero deliverables produced in the quarter.
- Scope overlap with an org-level AI practice forum that the CoP
  cannot usefully complement.

Dissolution is a steward decision, documented in a final brief and
sent to each participating team's manager.
```

### 2.2 One-page charter (post this in the wiki)

```markdown
# AI Practices CoP — One-page Charter

Purpose: share what we're learning about AI-assisted work so each
  team doesn't re-learn the same lessons alone.
Not: policy, procurement, performance review, incident response,
  mandatory training.

Scope: prompts, workflow patterns, review checklist evolution,
  shared evals, failure-mode taxonomies, tool-selection heuristics,
  fluency-growth artifacts.
Out of scope: policy / incident / hiring / vendor / org-wide
  workflow redesign.

Members: 6–10. One engineer and one manager per participating team;
  one from security / privacy / data if available. Two rotating
  stewards.

Rituals: monthly 90-min working session (fixed shape agenda);
  async channel with Friday curation.

Deliverables: pattern library · failure-mode digest · shared evals ·
  review-checklist proposals · (optional) quarterly brief.

Rules: disclose tool use; no PII; no named-individual performance
  discussion; escalate to governance / vendor / on-call when out of
  scope.

Sunset: two consecutive quarters of < 3 attendees or zero
  deliverables.

Review: quarterly. Next review: <date>. Charter version: 1.0.
```

### 2.3 Decision rationale (why these choices and not others)

- **Non-purposes before purpose, in impact.** The purpose section is three sentences; the non-purposes are five bullets. That ratio reflects reality: CoPs fail from scope creep, not from scope under-run. Naming the venues the CoP is *not* protects the venues it *is*.
- **Policy, procurement, performance, and incident response are explicitly outside.** Each of these has an owner elsewhere in the module stack: governance (mod-110), vendor/budget (mod-111), hiring/performance (mod-108), accountability/incident (mod-109). A CoP that quietly accretes any of them undermines those owners and burns the goodwill of the volunteers. NIST AI RMF **Govern 1.1** (clear roles and responsibilities) is the backbone reasoning here.
- **Target size 6–10.** This is small-group literature: once a working group passes ~10, the format must shift (sub-groups, structured facilitation). The charter sets the target range so the stewards have air-cover to say "we're full" without appearing exclusionary. The 6–10 range is a common working-group heuristic rather than a cited official standard — validate against the organisation's own working-group conventions before treating it as fixed.
- **Two rituals, no more.** Volunteer time is the binding constraint. One sync + one async + optional quarterly brief is the maximum workable load. Adding a weekly office hours slot or a reading group almost always kills the whole CoP within two quarters.
- **Fixed-shape agenda.** CoPs that improvise their agenda spend the first 15 minutes of each session figuring out the agenda. A pre-published fixed shape (10/20/30/20/10) is friction-eliminating.
- **Deliverables owned, not collective.** "The CoP will produce …" is not a plan. Each deliverable has an owner. If no owner is willing, the deliverable is not on the list.
- **Stewardship rotates.** Two stewards per two quarters. Permanent stewardship burns out the steward and freezes the practice; monthly rotation never builds momentum.
- **Sunset criteria are a feature, not a failure mode.** CoPs should be *willing* to die. A CoP that cannot dissolve becomes a zombie meeting. The sunset criteria (< 3 attendees or zero deliverables for two quarters) are deliberately generous and still trigger useful conversations when they hit.
- **Alignment with the handoff contract (Exercise 05).** If the organisation later appoints an org-wide enablement lead, the CoP's quarterly brief is the natural upstream input. The charter is drafted so handoff requires no restructure — just a reporting line added.

## 3. Validation steps

1. **Read the non-purposes to someone from each adjacent function** (governance lead, vendor owner, on-call lead, HR partner). They should each nod at their own non-purpose. If anyone says "but you'll end up doing that anyway," tighten the wording or the escalation rule.
2. **Test the joining protocol on one real candidate.** Walk a prospective member through steps 1–3. If it takes more than 10 minutes elapsed (ignoring the one-week steward turnaround), it is too heavy. If it is "just DM me," it is too light.
3. **Dry-run the monthly agenda on real content.** Pretend it is next month's session; bring one member's artifact, one pattern draft, one open-floor item. If the fixed shape leaves you with awkward time, re-cut the slots.
4. **Dogfood the deliverables.** Produce one entry in the pattern library using the template before the first session. If the template is confusing to its author, it will be confusing to every contributor.
5. **Rehearse an escalation.** Pick a hypothetical: an IC raises a policy question in the CoP channel. Walk through: who forwards it, to whom, with what artifact? If the chain is vague, add a named escalation contact in §7.
6. **Sunset self-check.** At the quarterly review, ask explicitly: "do the sunset criteria apply to us right now?" If the group reflexively protests without evidence, the criteria are being treated as ceremonial — which is exactly how zombie CoPs form.

Validation is behavioural: you are checking that the charter survives contact with volunteer time, scope creep, and adjacent-function sensitivities.

## 4. Rubric

Use this to grade a submitted CoP charter (self-assessment or peer review). Each criterion scores 0–3.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Purpose clarity** | Missing or vague | One-line generic purpose | Clear purpose + one example | Clear purpose + concrete example + alignment with program goal |
| R2 | **Non-purposes** | Absent | One non-purpose | 3+ non-purposes | 5 non-purposes covering policy / procurement / performance / incident / mandatory-training |
| R3 | **Scope boundaries** | Not stated | Inside only | Inside + outside | Inside + outside + named owner for each outside item |
| R4 | **Membership rules** | None | Open-ended "whoever wants" | Target size + joining rule | Target size + joining + leaving + stewardship rotation |
| R5 | **Rituals** | None | 1 ritual | 2 rituals (sync + async) | 2 rituals + fixed-shape agenda + silent-week clause |
| R6 | **Deliverables** | None | "Notes" | 2+ owned deliverables | Owned deliverables table + cadence + separation from personal journals |
| R7 | **Rules of engagement** | None | Disclosure only | Disclosure + confidentiality | Disclosure + confidentiality + no-named-individuals + escalation + openness default |
| R8 | **Escalation to other bodies** | Absent | Mentioned informally | Named owner per outside item | Named owner + referral format + example referral logged |
| R9 | **Sunset criteria** | Absent | "Review someday" | Explicit criteria | Explicit criteria + dissolution owner + final brief requirement |
| R10 | **Review cadence** | Absent | Ad-hoc | Quarterly | Quarterly + next-review date + version number in charter |

**Pass bar: 20/30.** Below that, iterate before publishing to the group.

## 5. Common mistakes

- **Writing the purpose and skipping the non-purposes.** This is the single highest-impact failure mode. Scope creep into policy, procurement, performance, or incident response is the most common reason CoPs stop being useful.
- **Opening membership to everyone who asks.** A 40-person CoP is a mailing list. The target range of 6–10 is not elitism; it is a working-group constraint.
- **Scheduling weekly sync meetings.** The CoP is volunteer time. Weekly synchronous meetings almost always die within a quarter; monthly syncs survive.
- **No fixed-shape agenda.** The first 15 minutes vanish into agenda negotiation, and the practice depth of each session is halved.
- **Collective ownership of deliverables.** "The CoP will produce a pattern library" without a named owner per entry produces nothing. Deliverable ownership is individual.
- **Making CoP attendance a performance signal.** Attendance becomes compulsory; the room fills with ICs who do not want to be there; the quality of discussion collapses. Keep membership voluntary — the charter should say so explicitly.
- **Accreting policy authority.** When the CoP ratifies a policy proposal "informally," it has stolen authority from the governance owner. Keep all policy work as *drafts that leave the CoP* and are ratified upstream.
- **Sunset-less charter.** Without dissolution criteria, zombie CoPs run on calendar inertia for years, consuming attention and producing nothing.
- **No rotation of stewards.** Permanent stewards burn out, and newer members never develop ownership. Two-quarter rotations are the minimum.
- **Pasting PII or named-individual commentary into the channel.** This is the single most common privacy failure in enablement groups. The rules of engagement must forbid it explicitly.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the **Govern** function (roles, responsibilities, information sharing) and **Govern 4.3** (organizational practices for information sharing) justify the CoP's structure as an *information-sharing* body with scope clearly distinct from governance, procurement, and incident response.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — the security framing for the rules-of-engagement section (no PII, no secrets in the channel) and for the failure-mode digest deliverable.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat taxonomy used by members when contributing failure-mode entries to the digest, ensuring the digest covers adversarial as well as accidental failure patterns.

<!-- follow-up: after two quarters of operation, replace the "target size 6–10" heuristic with the empirical attendance band your CoP actually sustains. Until then, the range is a working-group default rather than a cited figure. -->
