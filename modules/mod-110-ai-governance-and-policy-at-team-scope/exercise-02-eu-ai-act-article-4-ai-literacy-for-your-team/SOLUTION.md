# Exercise 02 — EU AI Act Article 4: AI Literacy for Your Team

> Module: `mod-110-ai-governance-and-policy-at-team-scope`
> Exercise type: **design** (worked literacy programme + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is a short, team-scope **AI literacy programme** that
satisfies the obligation in **Article 4 of the EU AI Act**: *providers
and deployers of AI systems shall take measures to ensure, to their
best extent, a sufficient level of AI literacy of their staff and other
persons dealing with the operation and use of AI systems on their
behalf, taking into account their technical knowledge, experience,
education and training and the context the AI systems are to be used
in, and considering the persons or groups of persons on whom the AI
systems are to be used.* Article 4's obligations became applicable on
**2 February 2025** across the EU, independent of AI-system risk tier.

The output is two artifacts:

1. `docs/AI_LITERACY.md` — the team's literacy programme, its scope,
   its syllabus, and how the team evidences that every relevant
   person has completed it.
2. `docs/onboarding/ai-literacy-checklist.md` — the completion
   checklist each IC signs (or digitally acknowledges) during
   onboarding and at the annual renewal.

Article 4 is deliberately a **principles-based**, not a
prescriptive, obligation. It does not name a curriculum, a test, or a
duration. What it does require: the measures must be *proportionate*
(taking account of staff's prior training, the AI systems in use, and
the persons the AI systems act on), the measures must actually
*happen* (they are enforceable), and the deployer must be able to
*evidence* that the measures happened.

A good team-scope programme is therefore:

- **Shaped by what the team actually does with AI**, not a generic
  "what is AI" deck.
- **Differentiated by role**: an IC who wires up an MCP server needs
  deeper coverage than a PM who writes a Jira ticket with the help of
  an assistant. The *"sufficient level"* bar is role-contextual.
- **Evidenced by a signed completion record**, dated, retrievable by
  the manager and by legal. "They were in the Zoom" is not evidence.
- **Renewed annually and triggered by material change** (new AI tool
  admitted, new incident, new regulation).
- **Contextual to the people on whom the AI is being used** —
  Article 4's clause "considering the persons or groups of persons on
  whom the AI systems are to be used" means a team whose output acts
  on job candidates (mod-108) needs a candidate-facing module; a
  team whose output acts on customers needs a customer-impact module.

Design decisions this solution encodes:

1. **The programme is role-tiered, not one-size.** Three tiers — User,
   Builder, Operator — map to what the person *does with AI*, not to
   their job title. A support engineer who runs an MCP-enabled agent
   is Operator-tier regardless of band.
2. **Each module names its learning objective in observable
   terms.** "Understand prompt injection" is not observable; "given a
   retrieval-augmented response that quotes user-supplied text, the
   learner identifies the injection surface" is.
3. **The programme is reused, not rebuilt from scratch per team.**
   The *module library* sits at the engineering-org level; each team
   selects the subset that fits its AI usage and *contextualises it
   with team-specific examples*. Article 4 requires proportionality,
   not duplicated effort.
4. **Completion is tracked in the repo, not in LMS screenshots.** The
   evidence that a person completed is a git-tracked line with name,
   date, and module list. That is auditable and reproducible.
5. **The programme is reviewed when the AI stack changes.** A new
   assistant or a new MCP server is a trigger for a programme
   refresh; a new incident is a trigger for an added module.
6. **The programme explicitly covers data, IP, and confidentiality
   (mod-110 exercise-04) and the team's incident runbook (mod-109
   exercise-01).** These are not optional literacy — they are the
   two things the manager will be asked about first in any post-hoc
   review.

## 2. Implementation — worked programme (`docs/AI_LITERACY.md`)

```markdown
# Team AI Literacy Programme — v1.0

**Team:** <team name>
**Owner:** <team manager name>
**Last revised:** 2026-11-12
**Next scheduled revision:** 2027-02-12 (quarterly)
**Off-cycle revision triggered when:**
- A new AI tool is admitted to the team's inventory (`docs/AI_INVENTORY.md`).
- An AI-adjacent incident is declared (SEV3 or higher).
- The EU AI Act, a Member State implementation, or a sectoral AI
  regulation applicable to the team's work is amended.
- A new team member joins.
- A member changes tier (User → Builder, Builder → Operator).

## 1. Purpose

This document is the team's implementation of the AI literacy
obligation in Article 4 of the EU AI Act for every person whose work
on this team involves the operation or use of AI systems on the
team's behalf.

Article 4 obliges the deployer (this team) to take proportionate
measures to ensure a sufficient level of AI literacy, taking into
account:
- The technical knowledge, experience, education, and training of
  the person.
- The context in which the AI systems are to be used.
- The persons or groups of persons on whom the AI systems are to be
  used.

This programme is the team's "measures". It is not legal advice; it
is implementation. Legal counsel has reviewed the structure (see §6);
counsel does not personally grade each completion.

## 2. Who is in scope

Everyone who, in the course of work on this team's behalf, operates
or uses an AI system — including but not limited to:

- Engineers using code assistants (Cursor, Claude Code, Copilot, etc.)
- Engineers wiring up or operating MCP servers.
- PMs, designers, and TPMs prompting assistants for drafting or
  analysis that enters team artifacts.
- Anyone operating a team-owned agent or retrieval index.
- Contractors and vendor on-site staff embedded in the team.
- Interns.

Out of scope: visitors, external auditors, candidates in interview
loops (they are "persons on whom" the system acts, not operators).

## 3. Tier assignment

The programme has three tiers. A person's tier is assigned by the
team manager at onboarding (or when scope changes) and is recorded on
their line in the completion log.

| Tier | Definition | Example |
|---|---|---|
| **User** | Operates an AI assistant whose outputs the person edits before use. No configuration authority, no agent/MCP ownership. | PM drafting a design review brief with an assistant. |
| **Builder** | Uses AI assistants to produce code or content that lands in a team artifact (PR, published doc, user-facing string), with normal review. | Most ICs on this team. |
| **Operator** | Configures or owns an agent, MCP server, retrieval index, or user-facing AI feature. Can expand the team's agent blast radius. | The person who maintains `AGENTS.md` for a team agent; the person who admits a new MCP server. |

Tier upgrades require completion of the destination tier's module
list before the elevated access is granted (the MCP policy and
`AGENTS.md` enforce this at the config level; this programme is the
training half of the gate).

## 4. Modules

Each module has: a *learning objective in observable terms*, a
*format*, a *duration*, a *required artifact or exercise* that
produces evidence, and a *reviewer*.

### 4.1 Required for all tiers (User, Builder, Operator)

- **M1 — The team's AI policy.**
  *Objective:* Given a hypothetical use case, correctly classify
  whether the team's AI policy (`docs/AI_POLICY.md`) allows it, and
  cite the specific section.
  *Format:* Read-through + three scenario questions answered in the
  checklist.
  *Duration:* 30 minutes.
  *Evidence:* Signed checklist line M1 + scenario answers reviewed
  by manager.

- **M2 — Data, IP, and confidentiality.**
  *Objective:* For each named data class in the team's data policy
  (see mod-110 exercise-04), state whether it may be sent to the
  team's AI assistants, and the reasoning.
  *Format:* Read-through + a three-row table the learner fills in.
  *Duration:* 30 minutes.
  *Evidence:* Filled table attached to the checklist, reviewed by
  manager and legal liaison (sample-based).

- **M3 — Trustworthy-AI characteristics.**
  *Objective:* Name the seven trustworthy-AI characteristics
  (valid and reliable; safe; secure and resilient; accountable and
  transparent; explainable and interpretable; privacy-enhanced; fair
  with harmful bias managed) and identify which the team's PR
  template enforces at review time.
  *Format:* 20-minute recorded talk + 2-question quiz.
  *Duration:* 30 minutes.
  *Evidence:* Quiz score ≥ 2/2 on the checklist.

- **M4 — Incident response touchpoints.**
  *Objective:* Given an AI-adjacent failure mode (hallucinated API,
  prompt injection suspected, user-visible regression), identify the
  routing path from the team's incident runbook (mod-109 ex-01) and
  the first action to take.
  *Format:* Three-scenario walk-through with the on-call lead.
  *Duration:* 45 minutes.
  *Evidence:* Three routed scenarios logged on the checklist.

- **M5 — Rights of the persons on whom the system acts.**
  *Objective:* Identify, for the team's user-facing AI outputs (if
  any), the user rights that apply (transparency, human review,
  redress, data access) and the specific team artifact that
  satisfies each.
  *Format:* 20-minute briefing by the team manager + Q&A.
  *Duration:* 30 minutes.
  *Evidence:* Checklist signature.

### 4.2 Required for Builder and Operator tiers

- **M6 — Review discipline for AI-assisted changes.**
  *Objective:* Given an AI-assisted PR, apply the team's review
  checklist (mod-103 ex-01), including checks for hallucinated
  identifiers, un-reviewed imports, over-fitted tests, and lack of
  author explanation. Identify at least two failure modes.
  *Format:* Guided review of two historical AI-assisted PRs from
  this team's git history (one that passed, one that was reverted).
  *Duration:* 60 minutes.
  *Evidence:* Written review comments on the two PRs linked from
  the checklist.

- **M7 — Confabulation and grounding.**
  *Objective:* Distinguish a grounded response (retrieval-backed,
  citation-present) from an ungrounded response, and apply the
  team's rule for when ungrounded output may ship.
  *Format:* Five example outputs (two grounded, three not); learner
  labels each and cites the policy.
  *Duration:* 30 minutes.
  *Evidence:* Labelled examples attached.

### 4.3 Required for Operator tier only

- **M8 — MCP tool-authorisation policy.**
  *Objective:* Given a proposed new MCP tool, place it in the
  correct tier of the team's MCP policy (mod-106 ex-03) and name
  the HITL gate that applies.
  *Format:* Two tiering exercises with the tool-admission reviewer.
  *Duration:* 60 minutes.
  *Evidence:* Written tiering rationale signed off by the MCP
  policy owner.

- **M9 — Prompt-injection and adversarial inputs.**
  *Objective:* Given a retrieval-augmented or web-fetching agent
  configuration, identify the injection surfaces (tool-call output,
  retrieved doc contents, user input, long-term memory) and the
  mitigation the team's policy applies to each.
  *Format:* Walk-through of the team's agent + tabletop of one
  attacker scenario.
  *Duration:* 90 minutes.
  *Evidence:* Written attack path documented and reviewed by
  secops liaison.

- **M10 — Decommissioning and rollback of an AI system.**
  *Objective:* Given a hypothetical scenario (model API vendor
  outage; prompt change caused user harm), execute the team's
  contingency plan (mod-110 ex-01 GOVERN 6.2).
  *Format:* Tabletop + runbook walk-through.
  *Duration:* 60 minutes.
  *Evidence:* Tabletop notes filed in
  `docs/governance-reviews/tabletops/`.

### 4.4 Role-contextual add-ons

The programme adds a module when the team's AI output acts on a
specific population. These are only added when the team's AI is
used in that context; otherwise they are skipped.

- **M11 — Candidate-facing AI (mod-108).** If the team uses AI in
  hiring loops, this module covers the team's rules on
  AI-assisted screening, resume review, and interview transcription
  (consent, retention, non-automation of final decision).
- **M12 — Customer-facing AI.** If the team ships generative output
  to customers, this module covers labelling obligations, content
  grounding, and redress path.
- **M13 — Health / financial / high-stakes decisioning.** If
  applicable (not for most teams); module content is domain-specific
  and is drafted with legal.

## 5. Delivery

- **New joiners** — M1–M5 required before any AI tool is enabled on
  their account. M6–M10 required within 30 days if they are
  Builder/Operator. Tracked in `docs/onboarding/completions.md`.
- **Tier change** — the destination tier's additional modules are
  required before the elevated access is granted.
- **Annual renewal** — every person on the team re-affirms the
  checklist annually. The re-affirm reviews the diff of the policy
  since their last affirm; where the diff is substantive (new tool
  admitted, new module added), the re-affirm includes completing
  the new module.
- **Off-cycle triggers** — see the frontmatter list.

Delivery format is deliberately low-ceremony: self-paced read +
written exercise + manager signoff. Classroom training is not
required; it is also not forbidden. What is required is the written
evidence.

## 6. Evidence and audit

Evidence lives in two files:

- `docs/onboarding/completions.md` — one line per person: name,
  tier, modules completed, dates. This file is the audit log.
- `docs/onboarding/ai-literacy-checklist.md` — the template each
  person fills in; completed copies are committed to
  `docs/onboarding/completed/<year>/<slug>.md`.

Audit queries this programme answers:

- *Who on this team is cleared to operate an MCP-enabled agent?*
  → `grep "tier: Operator" docs/onboarding/completed/**/*.md`.
- *When did person X last re-affirm?* → log file.
- *Which modules have a backlog of un-affirmed people after a
  policy update?* → quarterly governance review ritual (mod-110
  ex-01, GOVERN 1.5).

## 7. Who reviewed this programme

- **Team manager** — owner.
- **Engineering director** — reviewed 2026-11-11.
- **Legal liaison** — reviewed structure, sampled module content.
  Legal has not individually certified every module; the
  programme's design (role-tiered, context-specific, evidenced,
  renewed) is what legal signed off on.
- **Secops liaison** — reviewed M9 and the incident-response
  touchpoints in M4.

## 8. What this programme is not

- It is **not** a legal interpretation of Article 4. It is this
  team's implementation of Article 4. The legal interpretation is
  held by counsel and reflected in the escalation table
  (`docs/AI_ESCALATION.md`).
- It is **not** an AI research curriculum. Learners who want depth
  are pointed at external resources (cited in §9) but are not
  graded on them.
- It is **not** a one-time onboarding video. The whole point is the
  annual renewal and the off-cycle refresh.

## 9. References

- EU AI Act, Regulation (EU) 2024/1689, Article 4 (AI literacy).
  Applicable from 2 February 2025.
- NIST AI Risk Management Framework — GOVERN 2.2 ("training of
  personnel and partners"), which this programme implements at team
  scope.
- NIST AI 600-1 Generative AI Profile — specific risk categories
  (confabulation, prompt injection, IP, privacy) shaping modules
  M2, M7, and M9.
- OWASP Machine Learning Security Top 10 — the risk taxonomy
  referenced in M9 (prompt-injection and adversarial inputs).
- MITRE ATLAS — adversarial-threat taxonomy, cross-referenced in
  M9.
```

## 3. Validation steps

To validate a programme before publishing it:

1. **Does each module have an observable objective?** If the objective
   is "understand X", rewrite it until it is "given <stimulus>, the
   learner <does something the manager can grade>."
2. **Does each module produce evidence that is diff-reviewable?** A
   verbal nod is not evidence. A screenshot of a slide is barely
   evidence. A committed file with the learner's written answer is
   evidence.
3. **Are the tiers mutually exclusive and jointly exhaustive for the
   team?** Every person on the team should fit exactly one tier; if
   people are in between, add a tier or redraw the boundary.
4. **Does the Operator tier gate the MCP policy and `AGENTS.md`?**
   The literacy programme must be the training half of the gate that
   the config enforces — otherwise completion is decorative.
5. **Is there a trigger list for off-cycle refresh?** Programmes
   without off-cycle triggers go stale in the two months between
   quarterly reviews.
6. **Does the programme name the persons on whom the AI acts, where
   applicable?** M11–M13 are the Article 4 clause "considering the
   persons or groups of persons on whom the AI systems are to be
   used." Skipping them while the team ships candidate- or
   customer-facing AI is a gap.
7. **Can the team manager produce a completion report in under five
   minutes?** If not, the evidence format is wrong — fix it before
   anyone fills it in.
8. **Would a legal liaison sign off on the *programme design* rather
   than each module?** If yes, the programme is at the right level
   of ceremony; if the liaison wants to grade every answer, the
   programme is asking legal to do the manager's job.

## 4. Rubric

Grade a programme on the eight criteria below. 0–3 each. Pass bar:
**16 / 24**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| L1 | **Scope naming** | "Everyone" or "the team" | Named roles | Named roles + contractors + interns + visitors (and their exclusion rationale) | Scope + role tiering + rationale for tier assignment |
| L2 | **Learning objectives** | Vague ("understand", "be aware") | Some observable, some not | All observable | Observable + grader can score without the author in the room |
| L3 | **Evidence design** | No evidence collected | Attendance logged | Signed checklists committed to the repo | Signed checklists + filled exercises + diff-reviewable + audit query is one shell command |
| L4 | **Proportionality** | One-size-fits-all | Minor differentiation | Role-tiered, context-blind | Role-tiered + contextualised by "persons the AI acts on" (M11–M13 where applicable) |
| L5 | **Renewal + off-cycle triggers** | One-time only | Annual renewal only | Annual + "material change" vague | Annual + enumerated off-cycle trigger list + each trigger has a named owner |
| L6 | **Cross-module coherence** | Standalone | Mentions one other module | Hooks into `AGENTS.md`, data policy, incident runbook, MCP policy | All hooks are live hyperlinks in the repo, and the config enforces the completion gate (Operator-tier access requires Operator-tier completion) |
| L7 | **Legal review posture** | No legal involvement | Legal reviewed at author's request | Legal review of structure documented, sampling of content | Legal is a named reviewer in frontmatter + sampling cadence + legal did not write module content (that stayed with the team) |
| L8 | **"Not a legal interpretation" line** | Confuses implementation with interpretation | Implicit distinction | Explicit disclaimer | Explicit disclaimer + separate escalation table + reader can tell which is which |

## 5. Common mistakes

- **Treating Article 4 as a slide-deck obligation.** "We had a
  lunch-and-learn" is not evidence that staff are at a sufficient
  level of literacy. The programme has to generate something you
  can diff.
- **Writing the programme for the legal department.** Legal is a
  reviewer, not the audience. The audience is the ICs on the team.
  Programmes that read like contracts are programmes that nobody
  finishes.
- **One tier only.** A single-tier programme either over-trains the
  PM (who will skip) or under-trains the Operator (who will cause
  an incident). Article 4's "proportionate" clause is what makes
  tiering the right design.
- **Forgetting the "persons on whom" clause.** Teams that ship
  user-facing AI and treat literacy as an engineer-only concern
  have missed the clause. The modules on candidate-facing or
  customer-facing AI (M11–M13) are not optional if those outputs
  exist.
- **No annual renewal.** A programme with no renewal is a programme
  that will drift. The policy will change, the tools will change,
  the staff will change; the evidence will not.
- **Running the programme in an LMS the manager cannot query.**
  Vendor LMS platforms that do not expose completion data to the
  team are evidence black boxes. Keep the audit log in the repo,
  even if the content lives elsewhere.
- **Confusing training with certification.** Article 4 does not
  require a certification. It requires measures proportionate to the
  staff and the context. Adding a 20-question certification with a
  passing score is often *less* defensible than a filled-in
  exercise that demonstrates the person applied the policy, because
  the former is a memory test and the latter is a competence check.
- **Letting completion data drift from access.** If the MCP policy
  requires Operator-tier completion but completion is tracked in a
  separate system nobody checks, the gate is theatre. The
  completion log must be the thing the access reviewer looks at.

## 6. References

- **EU AI Act, Regulation (EU) 2024/1689, Article 4 (AI literacy).**
  Obliges providers and deployers to take measures to ensure a
  sufficient level of AI literacy of staff and others operating AI
  systems on their behalf, proportionate to technical knowledge,
  context of use, and the persons on whom the systems are used.
  Applicable from 2 February 2025. Published in the Official
  Journal of the European Union (12 July 2024).
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) —
  GOVERN 2.2 ("the organization's personnel and partners receive AI
  risk management training to enable them to perform their duties
  and responsibilities") is the structural anchor for the tiered
  training obligation; this programme is one team's implementation.
- [NIST AI 600-1: Artificial Intelligence Risk Management Framework —
  Generative AI Profile](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf) —
  the risk categories (confabulation, information security, IP,
  data privacy) that give the Builder and Operator modules their
  specific content (M7, M9).
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) —
  the shared taxonomy for the Operator-tier adversarial-input
  module (M9).
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat
  tactics and techniques; cross-referenced in M9 so attack-path
  documentation uses a shared vocabulary.

*Editorial note:* teams with staff in specific EU Member States
should check whether the national supervisory authority (e.g.,
France's CNIL, Germany's BfDI) has published supplementary
expectations on AI literacy evidencing; where such guidance exists,
reference it in the programme's frontmatter alongside Article 4.
