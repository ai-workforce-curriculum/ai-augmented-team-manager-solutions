# Exercise 01 — Empirical Research Into Team Policy

> Module: `mod-107-skill-growth-anti-atrophy-and-career-development`
> Exercise type: **design** (research plan + worked write-up + grading rubric)

## 1. Solution overview

The deliverable is a short, honest, **reproducible research note** that a team
manager can take to their next 1:1 with their skip and say: *"this is what is
actually happening to skill on my team under AI augmentation, and this is the
policy change I want to make because of it."* It is the input that stops team
manager AI policy from being a copy of a LinkedIn thread.

A good version of this write-up is:

- **Scoped to a question your team can answer in a week** — not a dissertation,
  not a vibe check. The question is small enough that a single IC, pair, or
  team can produce defensible evidence for it in five working days.
- **Citing inputs the reader can verify** — the team's own git history, PR
  metadata, incident log, 1:1 notes, promo packets, review tickets. External
  references are allowed and labelled.
- **Honest about what is signal and what is noise** — small-N observations are
  reported as such; the manager does not launder anecdote into policy by
  dressing it in statistics.
- **Policy-shaped at the end** — concludes with *what we will change, what we
  will watch, and what we will not change yet.*

Design decisions this solution encodes:

1. **The research precedes the policy, not the other way round.** It is tempting
   to reach for "deliberate practice carve-out" or "reset the ladder" because a
   blog post said so. A team manager does not write team policy against generic
   claims; they write it against *what their team is actually doing differently
   under AI augmentation.* This exercise forces that sequencing.
2. **Evidence must be reachable.** Every claim ties back to an artifact a peer
   could open: a diff, a ticket, a thread, a transcript. Un-reachable claims
   get an explicit `<!-- evidence-gap: ... -->` marker in the write-up; they
   do not become policy inputs until the marker is resolved.
3. **The research explicitly names skill-atrophy risks, not just productivity
   gains.** Pure throughput reporting is what the vendor's slide deck gives
   you. The manager's job is to also surface the *under-development* and
   *over-reliance* signals that productivity dashboards miss. This is the
   module's thesis — it belongs in the research note, not the follow-up.
4. **The write-up ends with a decision, not a recommendation to someone else.**
   Research without a policy change proposal is a book club. The team manager
   is the person who will own the policy change; the write-up is where they
   commit to it.
5. **The research plan is a reproducible protocol, not a one-shot narrative.**
   A new manager should be able to re-run the same protocol next quarter and
   get a comparable number.

## 2. Implementation — the research note

### 2.1 The protocol (what any team should run)

Pick **one** question from each column. The exercise is only tractable if the
scope is this tight.

| Dimension | Example questions you might pick |
|---|---|
| **Throughput / cycle time** | How did median time-to-merge for small PRs change after the team adopted Cursor + agent mode? |
| **Review and defect** | What fraction of AI-assisted PRs produced a hotfix or revert within 14 days vs. the team's baseline? |
| **Skill under-development** | On how many PRs in the last 60 days did the author struggle to explain their own diff in review? |
| **Over-reliance** | How often did an engineer escalate "the assistant gave a wrong answer" as if it were an external blocker, rather than a skill issue? |
| **Growth trajectory** | How did the bar on promo packets shift — are IC3s now routinely producing IC4-scope changes with AI assist, and is the panel calibrating for that? |

Then run the following protocol. One week, one research note.

1. **Scope the sample.** Pick a calendar window (e.g., last 60 days) and a
   population (e.g., the four ICs on your team who have adopted the assistant
   full-time). Write both down. If you cannot name both in one sentence, your
   scope is wrong.
2. **Enumerate sources before you look at any.** List the systems you will
   touch: Git log, PR metadata, CI logs, incident retros, 1:1 notes with the
   author's consent, review tickets, promo packets, exit interviews. Record
   what each system can and cannot answer. This list is part of the deliverable.
3. **Pull the data verbatim.** Export it to a working file. Do not reason off a
   summary you remember; reason off the raw rows. Keep the raw export; it is
   evidence.
4. **Label observations as evidence, pattern, or hypothesis.**
   - *Evidence:* one artifact, one claim. "PR #3421 reverted within 48 hours;
     author describes diff as `cargo-culted from Copilot suggestion` in the
     revert note."
   - *Pattern:* ≥ 3 artifacts supporting the same claim, drawn from
     independent sources. "Three PRs in the window show the author unable to
     answer `why this branch and not the other?` in review."
   - *Hypothesis:* a candidate explanation for the pattern. Explicitly
     tentative. "I suspect IC2 is skipping the exploration step because
     Cursor's completion closes the question too early."
5. **Name what is signal and what is noise.** Any N < 5 or any claim that
   relies on a single observer is noise and is labelled as such. A one-off
   incident is not an anti-atrophy signal; it is a data point you are
   watching.
6. **Make the policy ask.** End the note with: *what I will change, what I
   will watch, what I will not change yet, and the review date.*

### 2.2 Worked write-up (filled against a fictional team called "Platform API")

```markdown
# Empirical research: AI augmentation on Platform API — Q4 2026

**Author:** <team manager>
**Window:** 2026-09-10 → 2026-11-10 (sixty working days, 42 engineering days)
**Population:** 4 ICs (IC2 × 2, IC3 × 1, IC4 × 1). All adopted Cursor + Claude
Code in mid-September; prior to that, autocomplete only.
**Reviewers:** author + skip. 1:1 notes used only where the IC gave written
consent in-advance; see Appendix A.

## Question

Under full assistant adoption, is the team shipping faster, and are IC2s
still building the exploration and justification skills the ladder requires
at IC3?

This question is deliberately narrow. Broader questions ("is AI good for
us?") returned nothing publishable last time I tried.

## Sources used

| System | What it can answer | Known limits |
|---|---|---|
| GitHub PR + review API | Cycle time; review turnarounds; revert / hotfix link. | Does not label AI-assisted PRs unless the author discloses. |
| Linear | Ticket size, escalation count. | Self-reported story points; noisy. |
| Incident tool (FireHydrant) | Reverts and incidents in the window. | Only captures severity ≥ SEV3. |
| 1:1 notes (with consent) | Author's own account of exploration / struggle. | Self-report; not independently verifiable. |
| Promo packets drafted this cycle | What panels actually credited and discounted. | N = 1 (IC2 → IC3). |

Not used, and why:
- CI build logs: available but reconstructing "did the assistant write this?"
  from logs alone was not tractable in the time available. Resolution: we
  adopt a commit-message trailer (`AI-Assisted: yes` or
  `Co-Authored-By: <assistant> <noreply@…>`) so that `git log --grep` can
  filter, rather than continuing to tag by hand. Rolled into the PR
  template change below.

## Observations

### Throughput (evidence, not yet pattern)
- **Median time-to-merge for small PRs (< 200 LOC) dropped 31%** in the
  window: 2.4 days → 1.65 days (n = 68 small PRs). Source: GitHub PR API
  export, attached.
- **Median time-to-merge for large PRs (≥ 500 LOC) did not change**: 7.9 →
  7.8 days (n = 11 large PRs). Difference is within the noise of a small
  sample; reported as "no signal yet."

### Revert / hotfix rate (pattern)
- 4 reverts in the window across the four ICs. Prior-window baseline (same
  team, same ICs, 60 days): 2.
- Of the 4 reverts, 3 have the author describing the change in the revert
  note as "took the Cursor suggestion without reading" or equivalent. See
  PR #3421, #3488, #3501.
- **Pattern:** at least one failure mode is a known one — the IC accepted a
  completion that referenced an API shape that does not exist in our
  current dependency version. This is a direct hit on the review checklist
  item "every imported identifier exists in the current docs" (see
  mod-103/exercise-01).

### Exploration and justification in review (pattern)
- In **5 review threads** in the window, the IC could not answer "why this
  approach and not <alternative>" when the reviewer pushed.
- 4 of those 5 are the same IC (IC2-A).
- **Pattern** for this team, but **concentrated in one person.** I report
  it as a coaching conversation to have, not a team-wide policy signal yet.

### Promo packet calibration (observation, N = 1)
- IC2-A is being written up for IC3 this cycle. The draft packet leans
  heavily on throughput metrics. The exploration / justification gap I
  observed above is **not** captured in the draft.
- **Observation, not pattern.** But the fact that this gap is not even
  legible in the packet template is itself a signal that the template needs
  work — see exercise-03 and exercise-05.

### Over-reliance signal (evidence)
- One #help-platform-api thread where IC2-B escalated "the assistant said
  X" and asked a staff engineer to resolve. The staff engineer resolved it
  by reading the docs for 90 seconds. **Single data point.** I logged it
  but am not generalising.

## What I will change (policy)

1. **Introduce a 2-hour-per-week deliberate-practice carve-out for IC2s**
   (see exercise-02 for the scheduling protocol). The carve-out explicitly
   excludes assistant use for the duration, to rebuild the exploration
   muscle. Starts next sprint. Review in 90 days.
2. **Update the promo packet template** (see exercise-05) to require
   evidence of *explanation under adversarial review*, not only shipped
   artifacts. Current template silently rewards throughput.
3. **Add the "every imported identifier exists" item to our PR template**
   (already done in mod-103/exercise-01; I was slow to adopt it here).

## What I will watch (no change yet)

- **Over-reliance pattern.** One escalation is not a trend. If we see
  three in the next 30 days with the same shape, it becomes a 1:1 topic
  on every IC's next skip meeting.
- **Large-PR cycle time.** No signal in a small sample. Revisit at 90-day
  checkpoint with a bigger window.

## What I will not change yet

- **Ladder expectations themselves.** I have hypotheses that IC3 should
  now include "can defend AI-assisted work under adversarial review," but
  one data point on one promo packet is not enough. I open an ADR and let
  the broader org weigh in (see exercise-03).

## Appendix A — consent

Each IC named in this document reviewed the sections mentioning them before
I shared this upward. Written consent is in the `#team-admin` channel
pinned thread, dated 2026-11-09.

## Appendix B — references

- NIST AI Risk Management Framework — the Measure function's call to
  **document the sources, quality, and limits of data** used in AI
  decision-making is the structural reason the "Sources used" and "Known
  limits" table is required. See
  <https://www.nist.gov/itl/ai-risk-management-framework>.
- OWASP Machine Learning Security Top 10 — used downstream in the review
  checklist but not directly in this research note. See
  <https://owasp.org/www-project-machine-learning-security-top-10/>.
- MITRE ATLAS — used downstream in incident framing. See
  <https://atlas.mitre.org/>.
```

### 2.3 Why the write-up looks like this

- **One-page ethos.** A team manager reads this before a 1:1; a skip reads
  this before a monthly review. If it does not fit in one scrolling pane, the
  policy decisions inside it will not get read.
- **"Sources used" table is non-optional.** It is the piece most frequently
  cut by writers under deadline, and it is the piece that makes the note
  reproducible next quarter. The protocol in NIST AI RMF's *Measure* function
  — document provenance, quality, and limits of the data used — is the
  external anchor, but the *team-manager* reason is simpler: without it, your
  skip will quietly discount your conclusions.
- **"What I will not change yet" matters as much as what you will change.**
  Research that always produces a change is research that is being used to
  justify a change already decided. Naming the non-changes is how you
  demonstrate the opposite.
- **Consent appendix is not optional for 1:1-derived data.** Using a report
  from a 1:1 as public evidence without the IC's knowledge is a trust
  violation the manager will not recover from. One line of policy prevents it.

## 3. Validation steps

To validate a research note before taking it anywhere:

1. **Would a peer with your raw exports reach the same conclusion?** If not,
   the write-up is advocacy, not research. Rework it until a peer with the
   same exports would write something recognisably the same.
2. **Can every claim be re-opened from a hyperlink?** PR numbers, incident
   IDs, thread permalinks — any claim without a link is a claim that will not
   survive your skip's follow-up question.
3. **Does the note call out at least one thing it cannot answer?** Research
   notes that answer everything are not research notes.
4. **Does every N < 5 have the label "evidence, not pattern" or equivalent?**
   If a single PR is being used to justify a policy change, the note is
   wrong.
5. **Did the people named in the note see it before anyone else?** 1:1
   content used without pre-review is a trust break; this gate is a hard
   one.
6. **Is the policy ask specific enough to execute?** "We should think about
   doing something about X" is not a policy ask. "Starting next sprint, I
   will do Y; review in 90 days" is.

## 4. Rubric

Use this to grade a submitted research note (self-assessment or peer review).
Each criterion scores 0–3. Pass bar: **16 / 24**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Scope discipline** | Many questions, no window, no population | Window or population missing | Both named, scope fits a week of work | Window + population + why-this-question-not-another, all named up front |
| R2 | **Source table present and honest** | No source table | Systems listed | Systems + what each can answer | Systems + what each can answer + known limits + "not used, and why" |
| R3 | **Evidence vs. pattern labelling** | Claims float without labels | Some labels | Consistent labels | Every claim labelled and each claim's label is justified by N |
| R4 | **Skill-atrophy coverage, not just throughput** | Throughput only | Mentions atrophy | Names ≥ 1 atrophy/over-reliance signal with evidence | Names atrophy, over-reliance, growth trajectory, with distinct evidence for each |
| R5 | **Links and raw exports** | Few links; no raw | Some links | Most claims link to artifacts | Every claim links; raw export attached; `evidence-gap` markers used for gaps |
| R6 | **Policy ask** | None | "We should think about X" | Specific action + review date | Action + what-I-will-watch + what-I-will-not-change-yet + review date |
| R7 | **Consent and confidentiality** | 1:1 content used silently | Mentions consent in prose | Named consent for every 1:1 reference | Written consent, stored and dated, with named venue |
| R8 | **Reproducibility** | Not re-runnable | Protocol implied | Protocol stated | Protocol stated such that a peer manager could re-run it next quarter against their team |

## 5. Common mistakes

- **Starting from the policy, not the data.** Writing "we will adopt
  deliberate-practice carve-outs" and then hunting for evidence to justify
  it. The write-up becomes advocacy. Peers read it as advocacy. The policy
  rolls back inside a quarter.
- **Reporting vendor metrics as if they were your team's metrics.** "Cursor
  users ship 55% more PRs" is a vendor claim about an aggregate population.
  Your team is not that population. The write-up should contain *your team's
  numbers from your team's tools*, not a vendor's blog chart.
- **Using 1:1 content without consent.** Do this once and the team never
  tells you anything useful again. This is the single biggest career-level
  mistake available in this exercise.
- **N = 1 policies.** Changing team-wide practice because *one* IC had *one*
  bad PR with the assistant. One data point is evidence; it is not a
  pattern; it is not a basis for team policy. The write-up should name the
  observation and name the threshold at which it would become a policy.
- **Throughput tunnel vision.** Reporting only cycle time and merge rate
  means the note cannot distinguish "the team is faster" from "the team is
  faster but less able to defend the work in review." Both can be true
  simultaneously and the policy implications are opposite.
- **Un-reachable claims.** "Everyone's saying the assistant is making them
  worse at exploration." Everyone who? Saying it where? Un-reachable claims
  are rumours in a lab coat.
- **Confusing anti-atrophy with anti-AI.** The question is not "is AI bad for
  us?" (not falsifiable, not a research question). The question is a
  specific operational one about a specific skill in a specific population.
- **No review date.** A policy change without a review date is a policy
  change that will outlast its usefulness by three quarters.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) —
  the *Measure* function ("evaluate, analyze, or track identified AI risks")
  and in particular its emphasis on documenting the **sources, quality, and
  limits** of the data used in decisions, is the structural anchor for the
  "Sources used" / "Known limits" table and the evidence-vs-pattern
  discipline. The *Govern* function's accountability expectations are why
  the policy ask names a human and a review date.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) —
  cited here because the research note is an input to the team's broader
  AI-use policy; downstream policy exercises reference the OWASP risk
  categories directly.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat taxonomy;
  used to sanity-check that any security-relevant observations in the note
  (over-reliance on assistant output, prompt-content leakage from pasted
  context) are categorised against a shared vocabulary rather than invented
  locally.

<!-- follow-up: once the completeness pipeline has shipped a second cycle of
module solutions, add a worked example of the 90-day re-run of this protocol
so managers can see what the comparison looks like. -->
