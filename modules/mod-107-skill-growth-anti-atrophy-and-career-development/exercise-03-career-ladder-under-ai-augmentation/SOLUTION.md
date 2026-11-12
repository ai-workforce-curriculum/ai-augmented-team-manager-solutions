# Exercise 03 — Career Ladder Under AI Augmentation

> Module: `mod-107-skill-growth-anti-atrophy-and-career-development`
> Exercise type: **design** (worked ladder revision + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is a **revised career ladder** (or an addendum to the team's
existing one) for ICs working under heavy AI augmentation. Specifically, it is
the manager's first draft of the adjustments they would take to their skip,
their HRBP, and their peer managers for calibration. It is not a corporate
ladder rewrite; it is the team-level opening move that *forces the calibration
conversation to happen with real artifacts attached.*

A good revised ladder is:

- **Delta-first.** It states what *changes* at each level under AI
  augmentation and leaves the rest of the existing ladder alone. A
  from-scratch rewrite will not be adopted and should not be attempted
  solo.
- **Grounded in tasks, not tools.** It describes what the IC should be
  able to *do* and *defend*, not what tools they must use. A ladder tied
  to specific products goes stale as the vendor rotates.
- **Explicit about what moves up a level and what stays put.** If
  "produced a polished PR" moves from IC3 to IC2 because a competent IC2
  with Cursor now routinely produces one, the ladder must say so. If
  "designed and defended an architecture change" stays at IC4 regardless
  of tool, the ladder must say so.
- **Attached to evidence types.** Each level names the *kind of artifact*
  that counts as evidence at that level, so that promo packets can be
  judged against a shared standard.
- **Transparent about accountability.** Human-signed accountability for
  AI-assisted work is a line that runs up the ladder, with the weight
  rising at each level. This is the NIST AI RMF **Govern** function
  showing up in the ladder itself.

Design decisions this solution encodes:

1. **The ladder measures what the IC can *do unaided after assisted
   drafting*, not just what the assisted output looks like.** The core
   anti-atrophy principle: assistance shortens drafting, but defending the
   work, extending it under adversarial pressure, and explaining it to
   others remain the levels the ladder cares about.
2. **Level deltas flow downward as well as upward.** When a capability
   becomes cheap (e.g., producing a first-pass CRUD service), the ladder
   is updated so that doing that capability *does not* qualify someone at
   the old level. This is the uncomfortable half of the exercise; most
   draft ladders skip it.
3. **Mentoring and review weight rises with level.** At IC3+, the IC is
   expected to raise the floor of the people around them, not just their
   own output. Under AI augmentation the mentoring load becomes *more*
   important, not less — someone has to teach the IC2 which assistant
   outputs are right.
4. **Security, governance, and compliance show up in the ladder.** The
   senior IC is accountable for the risks that the OWASP and MITRE ATLAS
   categories describe. If the ladder does not say so, those risks are
   ambient — which means they are nobody's, which means they fail.
5. **The ladder is a draft to calibrate, not a decree.** It names the
   calibration venues (manager peer group, HRBP, promo panels) and the
   review date. The manager is opening a conversation, not winning one.

## 2. Implementation — the ladder delta

### 2.1 Scope and prerequisites

- The team is on an existing company ladder (IC1–IC6 in this worked
  example; a Staff role at IC5 and Senior Staff at IC6). The delta
  references the existing titles.
- The delta applies to ICs who spend ≥ 50% of their coding time inside an
  AI assistant (agent mode, chat, or heavy inline completion).
- The delta is published with an ADR that names the Exercise 01 research
  it responds to, so that peer managers can see the evidence base. The
  ADR is the artifact that goes to calibration; the ladder delta is the
  summary.

### 2.2 The delta table

```markdown
# Platform API ladder — AI-augmentation addendum, v0.1

**Scope:** ICs ≥ 50% assistant-use in their coding workflow.
**Status:** v0.1 draft. Calibration target: 2027-01-15.
**Owner:** <team manager>
**References:** Exercise-01 research note (Q4 2026), ADR-0014.

> This addendum is read alongside the existing company ladder. It changes
> **only** the AI-augmentation expectations at each level. Everything in
> the company ladder that this addendum does not mention is unchanged.

## IC2 (Software Engineer II)

**Changes at this level:**
- The expected output volume is **higher** than the pre-assistant ladder.
  A competent IC2 with full assistant use will ship small-to-medium PRs
  at roughly the rate a pre-assistant IC3 did. The ladder acknowledges
  this; a reviewer should not credit "shipped a lot" as above-level.
- The expected quality bar on small PRs rises accordingly. Specifically:
  no un-defended merges. "The assistant wrote it" is not a reason given
  in review; the IC2 is the author of record.
- A new explicit expectation: can explain *every* line of a merged diff
  in review. Lines the IC2 did not personally understand before merging
  are a ladder-level miss, not a style issue.

**Stays the same:**
- Still partners with IC3+ on design; is not yet expected to lead
  design.
- Still expected to seek review before merging anything risky.

**Evidence that counts:**
- Merged PRs with review threads showing the IC2 answering reviewer
  questions substantively (not "the assistant generated it").
- At least one write-up — bug investigation, small design, incident
  post-contribution — that was the IC2's own prose, not a one-shot
  assistant draft.

## IC3 (Senior Software Engineer)

**Changes at this level:**
- "Ships a lot of PRs" is **no longer** a differentiator from IC2. The
  ladder acknowledges this: the IC3 bar moves up, not sideways.
- New explicit expectation: catches ≥ 1 class of AI-authorship failure
  mode per quarter in review and writes it up so the team learns. Known
  classes include fabricated identifiers, inherited un-licensed code,
  plausible-but-wrong citations, prompt-injection-vulnerable templates
  — see Mod-103/exercise-06 for the OWASP-mapped reference list.
- Can **defend** the design of changes produced assistant-first — can
  answer "why this architecture and not <alternative>" without recourse
  to the assistant in real time. This is the behavioural signature of
  the exploration muscle Exercise 02's carve-out rebuilds.

**Stays the same:**
- Designs and ships medium-scope features independently.
- Participates in on-call; reviews others' PRs.

**Evidence that counts:**
- Review comments on others' PRs that caught AI-specific failure modes;
  the IC3's own ADRs that defend the chosen design against named
  alternatives; an incident where the IC3's catch prevented a
  production miss.

## IC4 (Staff Engineer — team level)

**Changes at this level:**
- Owns the team's AI-use standards. Not "has read them." Not "follows
  them." *Owns them.* The review checklist, the AGENTS.md, the OWASP
  mapping, the eval rubric, the accountability contract — the IC4 is
  the author of record for at least one, and contributes to the rest.
- Mentors IC2/3s on exploration and justification skill. Explicitly
  scheduled mentoring hours (see Exercise 02 — the IC4 carries a block
  in the carve-out schedule).
- Leads the team's post-incident write-ups for AI-adjacent incidents.
  "AI-adjacent" includes: the assistant generated code involved,
  prompts were part of the failing path, or the response was slowed by
  AI-tooling supply-chain issues. See Mod-109 for the postmortem
  practice.

**Stays the same:**
- Technical scope is still team-level, not org-level.
- Project leadership and cross-team glue remain central.

**Evidence that counts:**
- Named authorship of at least one team-level standard document.
- Mentoring relationships with measurable IC2/3 skill improvement (from
  Exercise 02's exit criteria).
- At least one AI-adjacent incident write-up they led.

## IC5 (Senior Staff Engineer — multi-team / org level)

**Changes at this level:**
- Shapes the organisation's AI-use policy, not only the team's. Drives
  the calibration conversation this ladder-addendum opens — across
  teams. Writes the ADRs that bind multiple teams.
- Can produce a defensible vendor / tooling recommendation under
  pressure: evaluating a model, an assistant, an eval harness. "We
  should try X because <influencer> endorsed it" is below-level at IC5.
- Carries the governance conversation at scale: NIST AI RMF
  *Govern*-function obligations show up at IC5 — risks named, owned,
  reviewed; residual risk documented.

**Stays the same:**
- Technical scope is multi-team / platform.
- Mentoring load scales with scope.

**Evidence that counts:**
- Cross-team standards documents with their name on them; ADRs that
  bound multi-team behaviour; a vendor / tooling evaluation that
  shipped with a quantified, review-able outcome.

## IC6 (Principal Engineer — org / industry level)

**Changes at this level:**
- Represents the team or the company to external venues on AI practice,
  with the mandate to speak accurately about what the organisation
  actually does — not marketing. Carries reputational weight for
  external accuracy.
- Plays an identifiable role in defining the next-cycle ladder
  revision. The ladder addendum is a v0.1 that an IC6 should be able
  to iterate into a v0.2 at org scope.

**Stays the same:**
- Industry-level scope, deep technical influence, multi-year
  architectural bets.

**Evidence that counts:**
- External talks, published artifacts, authored standards contributions
  that were recognisable to peer organisations.

## What this addendum does not say

- It does not list required tools. Tools rotate; the ladder is a
  multi-year artifact.
- It does not demand that an IC refuse AI assistance. "Refused to use
  the assistant" is not evidence of senior behaviour; defending a
  design is.
- It does not cover compensation, promo cadence, or performance
  management; those are company ladder concerns.
- It does not promise that the deltas above are correct. V0.1.
  Calibration date is a hard requirement, not an optional one.
```

### 2.3 Why these deltas and not others

- **"Ships a lot of PRs" stops counting at IC3.** The single most common
  calibration failure in 2026 is crediting assistant-amplified throughput
  as evidence of senior behaviour. The delta names the distinction: at
  IC3 and above, the ladder credits *what you can defend*, not *what you
  can draft*.
- **IC3 catches ≥ 1 class of AI failure mode per quarter.** This is the
  concrete, auditable version of "has senior judgement about assistant
  output." Without it, the level becomes "feels senior to the manager,"
  which is exactly the phenomenon panels push back on.
- **IC4 owns a standard document.** Ownership is more than authorship —
  it is "will defend the document at review, will revise it when the
  ground truth changes, will nag the team into following it." Without a
  named owner, the review checklist / AGENTS.md / OWASP map all decay.
- **IC5 shows up on vendor and tooling calls.** This is the level where
  the organisation makes bets that outlive specific engineers. Bets
  anchored in influencer opinion rather than evidence are a known IC5
  failure mode under assistant hype cycles. The delta names it.
- **Mentoring rises with level, not falls.** Under assistant augmentation,
  the IC4+ cost structure flips: the senior's time is more valuable *as
  a teacher of what the assistant cannot teach* than as a hand-coder.
  The delta aligns the ladder with that reality.
- **The exit of a `What this addendum does not say` section.** Explicit
  non-scope prevents the document from being wielded as a cudgel for
  things it never covered. (Same move as the accountability contract in
  Mod-103/exercise-07.)
- **Calibration is a named date.** V0.1 drafts that never calibrate are
  dead drafts.

## 3. Validation steps

To validate a ladder delta before taking it to calibration:

1. **Walk one recent promo packet through the new deltas.** Did the panel's
   decision survive? If a promotion that happened under the old ladder
   would *not* survive the new one, name it explicitly; that is where the
   calibration conversation needs to happen first.
2. **Walk one *missed* promo packet through the new deltas.** Did the
   absence survive? If someone passed over under the old ladder would now
   clearly qualify, that is a different and also-named conversation.
3. **Take the delta to two peer managers.** If their first reaction is
   "that's fine," they have not read it. Delta-first ladders should
   produce specific pushback; absence of pushback is absence of signal.
4. **Compare the deltas to the Exercise 01 research note.** Each delta
   should be traceable to an observation in the research or an explicit
   accountability expectation from the frameworks referenced. Deltas
   without this trace are the manager's instinct, which is fine, but
   should be labelled.
5. **Test the "stays the same" entries for honesty.** For each one, name a
   recent artifact that demonstrated that expectation under the new tool
   regime. If you cannot, you may have moved the ladder at that point
   without saying so.
6. **Confirm the calibration date is on at least two calendars.** The
   manager's, and the skip's, and ideally the HRBP's. Dates on only the
   author's calendar do not happen.

## 4. Rubric

Use this to grade a submitted ladder delta (self-assessment or peer review).
Each criterion scores 0–3. Pass bar: **16 / 24**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Delta-first discipline** | Full rewrite | Mix of delta and rewrite | Pure delta, scoped | Pure delta, scoped, with explicit "what this does not say" |
| R2 | **Downward deltas present** | Only upward | Mentions downward in prose | Names one downward delta | Names downward deltas at every level where new assistant capability shifts the bar |
| R3 | **Defence over drafting** | Credits drafting | Mentions defence | Defence named as level criterion ≥ IC3 | Defence named + evidence types for defence listed per level |
| R4 | **Mentoring / review scaling** | Not mentioned | Mentioned | Named at IC3+ | Named at IC3+ with specific IC2/3 mentoring expectations at IC4 |
| R5 | **Security and governance presence** | None | Mentioned | Named at IC4+ | Named at IC4+ + mapped to a framework (NIST AI RMF Govern / OWASP / MITRE ATLAS) |
| R6 | **Evidence types per level** | None | Some | Each level names evidence | Each level names evidence that could be observed in a real artifact |
| R7 | **Non-scope statement** | Missing | Vague | Clear | Clear + prevents a specific foreseeable misuse of the ladder |
| R8 | **Calibration plan** | None | "We'll discuss" | Named venue and date | Named venue + date + whose calendars it lives on + what counts as a successful calibration |

## 5. Common mistakes

- **Rewriting the whole ladder.** The team manager does not have the
  authority — and should not attempt — to replace the company ladder.
  The delta is the right unit of work. A full rewrite dies in review.
- **Only upward deltas.** The uncomfortable half of this exercise is
  saying that work that used to be IC3-defining is now routinely IC2
  work. Managers who skip this half are read as advocating for their
  team at calibration rather than calibrating them — the skip will
  notice, and the credibility of the delta suffers.
- **"The IC must use <tool>" as a criterion.** Ladders are multi-year
  documents; tools rotate. Tool-bound criteria rot visibly.
- **No security or governance presence.** Under the NIST AI RMF, risks
  that are nobody's are failed-by-default. If the ladder does not name
  who owns them, the team's governance layer is incomplete.
- **Mentoring as a flavour text.** "Supports others" as a soft bullet.
  Under assistant augmentation, mentoring rises in weight; the ladder
  should reflect that with specific, verifiable expectations (named
  carve-out blocks, documented IC2/3 skill-gain outcomes).
- **No non-scope statement.** The ladder gets used to fight battles
  (comp, promo cadence, performance) it does not cover. Writing down
  what it does not cover is cheap and effective.
- **Absence of calibration.** Shipping a ladder delta to one's team
  without calibrating it across peer managers produces a ladder that
  grades this team by different rules than the next team. HRBP will
  roll it back.
- **Treating "defence" as a vibe.** "The IC can defend their work" is
  not operational. The delta needs to say *how* defence is observed —
  in review threads, in ADR comments, in design reviews — otherwise the
  criterion is not auditable.
- **Writing the delta without the Exercise 01 note.** The ladder is
  downstream of the research. Without it, the deltas are the manager's
  priors dressed up as policy.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) —
  the *Govern* function's framing — risks named, owned, reviewed — is the
  structural reason the ladder names a human owner for AI-adjacent risks
  at IC4+ and frames external speaking responsibility at IC6. The *Map*
  function is the backdrop for the "evidence types per level" column: a
  level without observable evidence cannot be audited.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) —
  the ML-specific risk categories named at IC3+ (fabricated identifiers,
  license-poisoned snippets, prompt-injection-vulnerable templates) are
  grounded here. Senior ICs are accountable for catching these in review;
  the ladder names it.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat taxonomy;
  the IC4's incident-leadership responsibility includes placing
  AI-adjacent incidents against this taxonomy so post-incident learning
  is comparable across incidents.

<!-- follow-up: write a second-draft worked example that includes the
calibration round-trip — the peer manager pushback received, the deltas
the HRBP revised, and the resulting v0.2. That is the artifact that
closes the loop from "my draft" to "team policy." -->
