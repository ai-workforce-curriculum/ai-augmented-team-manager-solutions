# Exercise 07 — The Accountability Contract, v0.1

> Module: `mod-103-review-standards-and-eval-commissioning`
> Exercise type: **design** (worked contract draft + decision rationale + grading rubric)

## 1. Solution overview

The accountability contract is the module's capstone artifact: a short, team-owned document that states **who is accountable for what when AI assistance is involved in the team's work.** It ties together everything the earlier exercises produced — the review checklists, the AGENTS.md / Cursor rules, the ADR process, the commissioning rubric, the eval walkthrough, the OWASP mapping — into a single statement a new team member or external reviewer can read in five minutes.

The contract is **v0.1** on purpose. It is the first draft a team manager writes; it is intended to be revised with the team, signed (or at least acknowledged) by each member, and revisited after every significant AI-adjacent incident.

Design decisions this solution encodes:

1. **Accountability lives with a named human, not a tool.** This is the structural claim. Every risk the team runs, every artifact it ships, has a human accountable name attached. Tools are *used*; they are not *responsible*.
2. **The contract is short.** Target: one page printed, under 600 words of substance. Long contracts are not read; unread contracts are not contracts.
3. **It names practices, not aspirations.** "I disclose AI assistance in the PR description" is a practice. "I take AI quality seriously" is an aspiration.
4. **It attaches to artifacts already in the repo.** Each clause links to the review checklist, the AGENTS.md, the ADR process, the commissioning rubric, the eval suite, the OWASP mapping. The contract is the index; the earlier exercises are the chapters.
5. **It names what the team does *not* commit to.** Explicit non-scope prevents the contract from being used as a cudgel for things it never covered.
6. **It plans its own revision.** v0.1 contracts that never become v0.2 are dead documents. The revision cadence and the trigger for off-cycle revisions are both named.

## 2. Implementation — the v0.1 contract

```markdown
# Team AI Accountability Contract — v0.1

**Team:** <team name>
**Owner:** <team manager name>
**Last revised:** 2026-11-11
**Next scheduled revision:** 2027-02-11 (quarterly)
**Also revised when:** any AI-related incident of severity Medium or
higher is declared; any member joins or leaves the team.

## 1. What this contract covers

This contract describes what each team member — including the team
manager — commits to when AI assistants (coding assistants, writing
assistants, agents, LLM-backed features we ship) are involved in the
team's work.

It covers:
- Code we commit.
- Documents we publish internally or externally.
- Prompts and model wiring we ship into our products.
- Decisions we automate or delegate to agents.

It does **not** cover:
- Personal use of AI tools outside team artifacts.
- The team's compensation, headcount, or hiring decisions (those are
  covered by separate policies, including this curriculum's Mod-108 on
  hiring with AI in the loop).
- Vendor-level contracts with model providers (covered by procurement).

## 2. The non-negotiable commitments

Every member of this team — including me, the manager — commits to:

1. **I am the author.** Whatever I merge, send, or publish, I read and I
   can defend. An AI assistant drafted work does not transfer
   accountability to the tool. See the
   [AI PR / document review checklist](../exercise-01-ai-pr-review-checklist-authoring/SOLUTION.md).
2. **I disclose.** Non-trivial AI assistance on a PR, doc, or external
   artifact is disclosed in the artifact itself when it goes outside
   the team, and in the PR description when it stays internal. The
   model and the mode (chat, agent, inline edit) are named.
3. **I protect sensitive data.** I do not paste secrets, credentials,
   or customer identifiers into any AI assistant whose vendor contract
   does not allow it. When in doubt, I ask.
4. **I follow the review path.** Every AI-assisted merge or publication
   goes through the review checklist and, for high-risk artifacts, a
   second reviewer who also attests.
5. **I use standards-as-code, not vibes.** When a decision is
   non-trivial, I write (or link) an ADR before merging. I don't
   re-litigate decisions already captured as ADRs without writing a new
   one.
6. **I ship prompts and models through evals.** No production prompt or
   model change ships without the commissioned eval passing.
7. **I report near-misses.** When an AI assistant produced something
   that *almost* shipped and was caught in review, I write it up in
   `#team-ai-near-misses` so the checklist can learn.

## 3. The manager's additional commitments

As team manager, I further commit to:

1. **I maintain the artifacts this contract references.** The review
   checklists, the AGENTS.md, the ADR template, the commissioning
   rubric, the eval scaffolding, and the OWASP mapping are mine to keep
   current.
2. **I revise this contract on cadence.** Quarterly. And after any
   Medium+ AI-related incident. And when the team composition changes.
3. **I run blameless postmortems.** AI-caused or AI-related incidents
   use the blameless-postmortem format from the module on accountability
   and incident response. The incident report names systems and
   practices; it does not name an individual as the cause.
4. **I protect learning time.** Members have scheduled time to improve
   their own AI fluency. Over-reliance is a failure of training, not of
   character.
5. **I will not use AI output as a substitute for feedback.** Written
   performance feedback is from me. Summaries drafted by AI are labelled
   and reviewed before I send.
6. **I escalate when the contract and a business ask conflict.** If
   leadership asks for a shipping tempo the contract cannot sustain, I
   bring the conflict up rather than silently drop clauses.

## 4. Allocation of responsibility at merge time

| Role | Responsibility |
|---|---|
| PR author | Attestation per §2.1–§2.4; running the review checklist; linking the eval if a gate applies. |
| First reviewer | Reading the diff themselves; checking every ticked box is honestly ticked. |
| Second reviewer (high-risk only) | Independent read; attesting to the OWASP-mapped categories relevant to the change. |
| Decision owner (for commissioned evals) | Approving the pass / fail / judgement-zone call per the commissioning rubric. |
| Team manager | Reviewing contracts quarterly; updating standards-as-code; owning incident response. |

## 5. What the team does not do

So that this contract is not misread into forbidding too much, we
explicitly do not commit to any of the following:

- We do not forbid personal use of any specific AI tool.
- We do not promise a specific AI-tool allowlist. Tool selection is
  reviewed quarterly with procurement and is out of scope for this
  contract.
- We do not promise that AI-assisted work will be free of mistakes.
  The commitment is to the process that catches mistakes, not to
  perfection.
- We do not commit to open-sourcing our prompts, evals, or review
  checklists outside the team.

## 6. Signing / acknowledgement

Every member of the team acknowledges this contract by commenting
`acknowledged: v0.1` on the PR that introduces it. New members
acknowledge it as part of the onboarding checklist.

Acknowledgement is not a legal signature; it is a social commitment
that the member understands and consents to operate under this
contract. Dissent — a member who disagrees with a clause — is expected
to surface in review, not after.

## 7. Change log

| Version | Date | Author | Summary |
|---|---|---|---|
| v0.1 | 2026-11-11 | <team manager> | First draft, derived from module `mod-103` exercises. |
```

### 2.2 Decision rationale

- **Human-named accountability as §2.1.** The structural claim ("I am the author") is deliberately clause 1. If any clause is cut in a later revision, 2.1 is the one that must survive — it is the only clause that cannot be bought back later.
- **Disclosure (§2.2) distinguishes internal from external.** External disclosure protects audience and reputation; internal disclosure feeds the near-miss log and the review-checklist refinement loop. Conflating the two overburdens one case and under-serves the other.
- **"I ship prompts through evals" (§2.6) is in the member-level section, not the manager-only section.** The eval gate is not a manager-only concern. If engineers believe the gate is "the manager's thing" they'll game it. Making it a member commitment binds the whole team.
- **Near-miss reporting (§2.7) is explicit.** Near-misses are the single highest-value signal for improving the review checklist, and are the hardest to collect without a stated commitment. Making it a clause is how it survives contact with a busy sprint.
- **Manager's "I protect learning time" clause (§3.4).** Teams that fail with AI tools often fail at skill-growth, not at tool-selection. Making the manager's protection of learning time a signed commitment is upstream of most of the other clauses.
- **"I will not use AI output as a substitute for feedback" (§3.5)** is here because performance feedback is the most consequential, highest-trust-cost artifact a manager produces. The clause exists to make clear that an AI-drafted 1:1 summary is a draft, not a judgement.
- **§5 (what the team does not do)** prevents the contract from being misread into a tool ban or an open-source obligation. Explicit non-scope is a kindness to future readers.
- **§6 acknowledgement by PR comment.** Simple, auditable, versioned. A full-signature system is theatre for a document that is explicitly v0.1.
- **§7 change log.** The contract is a document the team will revise; the change log is where that revision is visible. Without it, versioning is prose.

The contract maps to governance frameworks:

- **NIST AI RMF, Govern function.** The contract operationalises **Govern 1.1 (responsibilities documented)**, **Govern 1.4 (enforcement mechanisms)**, **Govern 3.2 (team roles and responsibilities)**, and **Govern 4 (accountability and transparency)**.
- **Govern 5 (engagement with AI actors)** is implicit in the manager's §3.6 clause on escalation.
- **Measure and Manage functions** are not the contract's job; they are the job of the eval scaffolding (Exercise 05) and the incident-response process the contract references.

## 3. Validation steps

1. **Can a reviewer read it in five minutes?** Time yourself. If it takes longer, cut.
2. **Can every clause be observed?** For every "I commit to…" clause, name the artifact or behaviour you'd observe to tell whether it was kept. Clauses that cannot be observed are aspirations; demote or delete them.
3. **Walk the clause-to-artifact map.** Every §2 and §3 clause should link to a concrete artifact (checklist, AGENTS.md, ADR, rubric, eval, mapping). If a clause has no artifact, either (a) build it or (b) drop the clause for v0.1.
4. **Blind review.** Hand the draft to a peer manager from a different team. Ask: "if your team adopted this verbatim, what would you have to change?" Their answer is the diff between generic and your-team-specific; address it in v0.2.
5. **Dissent dry-run.** Before shipping v0.1, ask the most senior IC on the team: "which clause would you push back on?" If they say "none," either you've written an anodyne contract or they haven't read it. Both are problems.
6. **Incident replay.** Take one past non-AI incident; rewrite it as an AI-related one. Which clause would have prevented it, blunted it, or clarified ownership? If no clause bites on the hypothetical, the contract is too soft on the practice that caused the incident.
7. **Schedule the revision.** The *next revision* date must be a calendar entry, not a note. A contract that never gets a v0.2 has stopped being a contract.

## 4. Rubric for grading a submitted contract

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Length discipline** | > 1200 words | 800–1200 | 400–800 | 300–600, dense, one page |
| R2 | **Human-named accountability in clause 1** | Missing | Implied | Stated | Stated + examples + contrast with tool-as-author framing |
| R3 | **Member vs. manager commitments split** | Flat list | Two sections but overlapping | Clearly split | Clearly split + each clause lives at the right level |
| R4 | **Artifact linkage** | None | Some mentions | Each clause names an artifact | Each clause links to a repo-committed file |
| R5 | **Non-scope section** | None | One line | Explicit list | Explicit list + reason per item |
| R6 | **Revision cadence** | None | Calendar entry | Cadence + incident trigger | Cadence + incident trigger + join/leave trigger + change log table |
| R7 | **Observability** | Many aspirational clauses | Mixed | Most clauses observable | Every clause observable; non-observable ones demoted |
| R8 | **Acknowledgement mechanism** | None | Prose "we agree" | Named mechanism | Named mechanism + onboarding hook |
| R9 | **Integration with earlier exercises** | None | Mentions | References | Each §2 and §3 clause maps to artifacts from Exercises 01–06 |

**Pass bar: 20/27.**

## 5. Common mistakes

- **Writing a values document, not a contract.** "We value curiosity and craft" is not a clause. "I read every line of every diff I merge" is.
- **Making it too long.** v0.1 contracts over 1200 words don't get read. Cut.
- **Naming no human.** "The team is responsible for X" is softer than naming the role that owns X. Use roles, name humans where possible.
- **No non-scope section.** The contract gets weaponised. A one-paragraph §5 prevents that.
- **No revision trigger.** A "quarterly" cadence without an incident-driven off-cycle trigger means the contract learns from calendar only, not from reality.
- **Missing the manager's own commitments.** A contract that binds members and not the manager is noise. §3 is non-optional.
- **Acknowledgement as a legal signature.** A heavy mechanism breeds avoidance. A PR comment is enough for v0.1.
- **Clauses that duplicate HR policy.** Compensation, hiring, discipline live elsewhere. §5 says so.
- **Never shipping v0.2.** A v0.1 that doesn't become v0.2 was a one-off deliverable, not a contract. The change log table in §7 is the reminder.
- **Assuming the contract fixes a culture problem.** It doesn't. It expresses the culture the team is trying to be. If no one in the team believes clause §2.7, writing it down won't make them report near-misses.
- **Letting an AI assistant author the contract unreviewed.** Ironic, but real. The accountability contract itself is a High-risk artifact by this module's own rubric; it must be author-attested and read by every member.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the Govern function (especially Govern 1, 3, and 4) is the direct backbone of this contract's accountability design.
- [OWASP Top 10 for Large Language Model Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — motivates the sensitive-data clause (§2.3) via LLM06, the eval-gating clause (§2.6) via LLM01/LLM09, and the near-miss reporting clause (§2.7) via LLM08 Excessive Agency.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — supplies the broader ML-risk framing referenced in Exercises 03 and 06, to which this contract points.
- [MITRE ATLAS](https://atlas.mitre.org/) — used as a sanity-check that the contract's non-negotiable commitments cover the adversarial-threat categories most relevant to a team shipping LLM-assisted work.
- Exercises 01–06 (this module) — review checklists, AGENTS.md / Cursor rules, ADR / lint package, commissioning rubric, Promptfoo / LangSmith walkthrough, and OWASP mapping; each is referenced by the contract and forms its implementation surface.

<!-- follow-up: whether your organisation requires accountability contracts to be versioned alongside employment agreements or in a separate repository. The default here (repo-versioned, PR-acknowledged) is appropriate for an individual team and may not satisfy enterprise legal requirements — verify with legal before escalating this draft beyond the team. -->
