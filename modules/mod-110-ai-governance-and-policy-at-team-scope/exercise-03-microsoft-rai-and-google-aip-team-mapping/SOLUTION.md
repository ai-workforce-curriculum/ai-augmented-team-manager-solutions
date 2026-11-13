# Exercise 03 — Microsoft RAI and Google AI Principles: Team-Scope Mapping

> Module: `mod-110-ai-governance-and-policy-at-team-scope`
> Exercise type: **design** (worked principle-to-practice mapping + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is a **mapping document** that takes the two most
widely-referenced practitioner principle sets — **Microsoft's
Responsible AI principles** and **Google's AI Principles** — and binds
each principle to concrete team-scope artifacts and rituals the team
already maintains.

Microsoft's six Responsible AI principles are:

1. Fairness
2. Reliability and safety
3. Privacy and security
4. Inclusiveness
5. Transparency
6. Accountability

Google's AI Principles, as published on `ai.google`, cover Bold
innovation, Responsible development and deployment (including safety,
security, privacy, scientific excellence, accountability), and
Collaborative progress; the core practice obligations distilled out of
them in standard engineering interpretations are:

- Be socially beneficial.
- Avoid creating or reinforcing unfair bias.
- Be built and tested for safety.
- Incorporate privacy design principles.
- Uphold high standards of scientific excellence.
- Be accountable to people.
- Be made available for uses that accord with these principles.

*Editorial note:* Google has revised its AI Principles multiple
times (most recently in early 2025); before publishing the team's
mapping document, confirm the current wording and the specific
commitments at https://ai.google/responsibility/principles/ against
the labels used here, and record the revision date in the
mapping's frontmatter.

A team-manager-scope mapping is not an ethics essay. It is a
*routing document*: for each principle, it says **which team artifact
the principle lives in**, **which review event enforces it**, and
**which evidence would be produced if someone asked.** Principles are
only credible at the team level if they decompose into practice.

The output is `docs/AI_PRINCIPLES_MAP.md`. It serves three readers:

- The team manager, to plan what still needs to be built or
  reviewed.
- A peer reviewer or director, to see at a glance what principle
  commitments the team can show evidence for.
- A legal / compliance / security partner, to find the right artifact
  without re-interviewing the team.

Design decisions this solution encodes:

1. **One mapping table per principle set, with a merge view.** Keep
   each company's principles in their native wording to avoid
   mis-citation. Then add a merge column showing the single team
   artifact that answers both — because principle sets overlap
   heavily and the team should not maintain duplicate controls.
2. **"Artifact + enforcement event + evidence" triples.** A principle
   that only lives in a policy document but has no enforcement event
   (PR review, eval gate, incident post-mortem) and no evidence
   (test output, review comment, log row) is theatre.
3. **Explicit "not yet" rows.** Some principles have no team
   enforcement today. Those rows are kept visible as *"commitment
   held; team control pending"* with an owner and a date. Hiding
   gaps produces a map that is wrong by omission.
4. **No new vocabulary.** The team already owns `AGENTS.md`, PR
   templates, incident runbooks, delegation contracts, MCP policies,
   literacy programmes, data policies. The mapping *points to those*,
   it does not create a parallel ethics vocabulary.
5. **The mapping is reviewed when either company updates its
   principles.** Both Microsoft and Google have revised their
   principles multiple times. The review trigger is in the
   frontmatter.
6. **The mapping is not a cheat code to claim alignment.** The
   principle sets are not law; mapping the team's practice to them
   is a way to *explain* practice to a wider audience, not a way to
   *assert* the team is virtuous. That tone matters.

## 2. Implementation — worked mapping (`docs/AI_PRINCIPLES_MAP.md`)

```markdown
# AI Principles Map — v1.0

**Team:** <team name>
**Owner:** <team manager name>
**Last revised:** 2026-11-12
**Next scheduled revision:** 2027-02-12 (quarterly)
**Off-cycle revision triggered when:**
- Microsoft publishes a revision to its Responsible AI principles
  or Responsible AI Standard.
- Google publishes a revision to its AI Principles on
  ai.google/responsibility/principles/.
- The team admits a new AI tool or ships a new user-facing AI
  feature.
- An AI-adjacent incident is declared and a post-mortem concludes
  that a principle was not operationalised.

## 1. Purpose

This document maps two widely-referenced AI principle sets —
Microsoft's Responsible AI principles and Google's AI Principles —
to the team's existing artifacts and review events. It is a *routing
document*: for each principle, it names the specific artifact
where the commitment lives, the enforcement event that produces
evidence, and the file or system where evidence is stored.

It is **not**:
- A legal interpretation.
- A claim that the team is compliant with any regulation.
- A duplication of the team's policies — it points to them.

## 2. How to read the tables

Each row is a triple:

1. **Artifact** — the specific file or system in the team's repo or
   tooling where the commitment lives.
2. **Enforcement event** — the review, ritual, or automated gate
   that would catch a violation of the commitment at team scope.
3. **Evidence** — the trailing artifact a reviewer could point at to
   say "this commitment is being met."

A row with no enforcement event and no evidence is marked
*"commitment held; team control pending"* with an owner and a
target date. These rows are visible so they can be resolved.

## 3. Microsoft Responsible AI principles → team practice

### 3.1 Fairness

AI systems should treat all people fairly.

| Scope | Artifact | Enforcement event | Evidence |
|---|---|---|---|
| **Internal (team-developer-facing AI, e.g., code assistant)** | `docs/AI_POLICY.md` §Fairness; the team's review checklist (mod-103 ex-01) requires that any assistant-produced change affecting user-visible classification or personalisation is flagged for fairness review. | PR review gate. | PR comment trail; issue label `ai-fairness-reviewed`. |
| **External (user-facing AI output)** | If the team ships user-facing AI output, `docs/evals/fairness/` contains fairness fixtures (e.g., class-conditional output rate checks) that gate release. | CI eval run on release branch (mod-103 ex-02). | Eval output committed per release; failing fixtures block merge. |
| **Internal (hiring-related AI, mod-108)** | `docs/HIRING_AI_POLICY.md` explicitly forbids automated adverse decisioning; human-in-the-loop for every candidate-impacting assist. | Hiring loop sign-off. | Hiring debrief notes. |

### 3.2 Reliability and safety

AI systems should perform reliably and safely.

| Scope | Artifact | Enforcement event | Evidence |
|---|---|---|---|
| Evaluations | `docs/evals/` — the team's eval suite with fixtures covering golden paths, regression fixtures from past incidents (mod-109 ex-05), and adversarial fixtures. | CI eval gate on main-blocking changes. | `evals/results/<date>.json` committed per run. |
| Rollback | `docs/AI_CONTINGENCY.md` (mod-110 ex-01 GOVERN 6.2) with a named fallback for every third-party AI dependency. | Annual drill. | Drill notes in `docs/governance-reviews/tabletops/`. |
| Incident response | `docs/INCIDENT_RUNBOOK.md` (mod-109 ex-01) with AI-specific signal routing. | Runbook drill; incident declaration. | Post-mortems in `docs/postmortems/`. |

### 3.3 Privacy and security

AI systems should be secure and respect privacy.

| Scope | Artifact | Enforcement event | Evidence |
|---|---|---|---|
| Data handling | `docs/AI_DATA_POLICY.md` (mod-110 ex-04) names the data classes and which AI tools may receive each. | Pre-commit hook scanning for data-class patterns; PR review item. | Blocked-paste logs; review comments. |
| MCP tool authorisation | `docs/MCP_POLICY.md` (mod-106 ex-03) tiers every tool and names HITL gates. | Agent config lint; per-use confirmation for T2+. | Agent config checked in; audit log. |
| Credentials | `docs/MCP_POLICY.md` §Credentials; rotation schedule per server. | Rotation calendar. | Rotation log. |
| Prompt injection | Literacy programme M9 (mod-110 ex-02) + retrieval pipeline review. | PR review gate for retrieval changes; secops liaison review. | Attack-path doc per retrieval change. |

### 3.4 Inclusiveness

AI systems should empower everyone and engage people.

| Scope | Artifact | Enforcement event | Evidence |
|---|---|---|---|
| Accessibility of user-facing AI output | `docs/evals/accessibility/` — fixtures covering screen-reader output, plain-language output, and alt-text generation (if applicable). | CI eval gate. | Eval results. |
| Internal access — who gets to use the team's agents | Literacy programme tiering (mod-110 ex-02); every IC on the team is at least User-tier so assistant use is not reserved for a subset. | Onboarding. | Completion log. |
| Feedback intake | `docs/AI_FEEDBACK_INTAKE.md` (mod-110 ex-01 GOVERN 5.1) collects feedback from users of the team's AI outputs and routes it to the eval suite or policy. | Weekly triage. | Feedback log with disposition. |

### 3.5 Transparency

People should understand how AI systems make decisions that affect them.

| Scope | Artifact | Enforcement event | Evidence |
|---|---|---|---|
| Labelling user-facing AI output | `docs/AI_POLICY.md` §Labelling — every user-facing AI-generated artifact is labelled or distinguishable. | PR review; UI copy review. | Screenshots; review comments. |
| Internal attribution of AI-assisted work | `docs/AI_POLICY.md` §Attribution — commit trailers (`AI-Assisted: yes` or equivalent) on AI-assisted work. | Pre-commit hook or review item. | Git log grep. |
| Model + prompt disclosure (when user asks) | `docs/AI_POLICY.md` §Disclosure — if a user asks "was this written by AI?", the team's named answer and routing. | Customer-support playbook. | Support thread transcripts. |

### 3.6 Accountability

People should be accountable for AI systems.

| Scope | Artifact | Enforcement event | Evidence |
|---|---|---|---|
| RACI | `docs/AI_RACI.md` (mod-110 ex-01 GOVERN 2.1) names the responsible/accountable/consulted/informed party per class of change. | Quarterly governance review. | ADR header lists reviewers. |
| ADRs | `docs/adr/` — every AI-adjacent decision is a numbered ADR. | Per-decision. | The ADR file itself. |
| Escalation | `docs/AI_ESCALATION.md` (mod-110 ex-01 GOVERN 2.3). | On-call playbook. | Escalation thread transcripts. |

## 4. Google AI Principles → team practice

The team's mapping uses the publicly-documented principle wording as
of the version linked in §7. See the editorial note above
about keeping the wording current.

### 4.1 Be socially beneficial

| Artifact | Enforcement event | Evidence |
|---|---|---|
| Product review process for new AI-touching features (team + PM + design) — assesses benefit vs. risk. | Pre-launch review. | Launch review deck + ADR. |
| For internal-only tooling, the team's monthly AI practice brief (mod-110 ex-01 GOVERN 4.2) notes beneficial vs. problematic use patterns observed. | Half-yearly brief. | `docs/ai-practice-briefs/`. |

### 4.2 Avoid creating or reinforcing unfair bias

Same as Microsoft §3.1 above. The team maintains **one** fairness
practice (see §5 merge view), not two.

### 4.3 Be built and tested for safety

Same as Microsoft §3.2 above.

### 4.4 Be accountable to people

Same as Microsoft §3.6 above.

### 4.5 Incorporate privacy design principles

Same as Microsoft §3.3 above.

### 4.6 Uphold high standards of scientific excellence

| Artifact | Enforcement event | Evidence |
|---|---|---|
| `docs/evals/` — eval design references the measurement literature; hypotheses are falsifiable; results are logged. | Eval design review. | Eval design docs; results. |
| Quarterly research-into-team-policy note (mod-107 ex-01) sets the discipline for evidence-vs-pattern labelling and reproducibility. | Quarterly. | Research notes in `docs/research-notes/`. |

### 4.7 Be made available for uses that accord with these principles

| Artifact | Enforcement event | Evidence |
|---|---|---|
| `docs/AI_POLICY.md` §Permitted-uses — the team's named list of uses that are allowed, restricted, or forbidden. | Policy review on each new feature. | ADR + review notes. |
| Tool admission (mod-110 ex-01 GOVERN 6.1) — new third-party AI tools enter via an admission review that evaluates alignment with the team's policy. | Admission review. | `docs/tool-admissions/<tool>.md`. |

## 5. Merge view — single-control cross-reference

For each principle pair that overlaps, the team maintains **one**
control and links it twice. This table is what the team actually
operates; §3–§4 are what each company's principles are called.

| Team control (single artifact) | Microsoft principle | Google principle |
|---|---|---|
| Fairness evals + hiring HITL | 3.1 Fairness | 4.2 Avoid unfair bias |
| Eval suite + rollback + incident runbook | 3.2 Reliability and safety | 4.3 Safety |
| Data policy + MCP policy + credentials rotation + prompt-injection review | 3.3 Privacy and security | 4.5 Privacy |
| Labelling + attribution + disclosure | 3.5 Transparency | Covered under 4.4 Accountability |
| RACI + ADRs + escalation | 3.6 Accountability | 4.4 Accountability |
| Accessibility evals + inclusive access | 3.4 Inclusiveness | (not explicit; mapped to the 4.1 social-benefit review) |
| Research-into-team-policy ritual + eval design discipline | (not explicit; mapped to 3.2 reliability's testing commitment) | 4.6 Scientific excellence |
| AI policy §Permitted-uses + tool-admission review | (covered under 3.6 Accountability) | 4.7 Availability of use |

## 6. Rows currently marked "commitment held; control pending"

| Row | Why it is pending | Owner | Target |
|---|---|---|---|
| `evals/accessibility/` fixtures | Team does not currently ship user-facing AI output requiring alt-text; placeholder fixtures only. | <IC name> | On first customer-facing feature using generative output. |
| Hiring AI (`docs/HIRING_AI_POLICY.md`) | Team has not yet used AI in hiring loops; policy drafted but not yet exercised. | <manager> | On first AI-assisted hiring loop. |
| Customer-facing labelling | Team ships no customer-facing generative output today. | PM + manager | On first customer-facing generative feature. |

Making these rows visible is the point. The map is not a trophy
cabinet; it is a punch list.

## 7. References

- [Microsoft Responsible AI](https://www.microsoft.com/en-us/ai/responsible-ai) —
  the six-principle set
  used in §3. Microsoft also publishes the Responsible AI Standard
  (v2, 2022) which decomposes the principles into goals and
  controls at organisation scope; the goals in that standard are
  the inspiration for the artifact-level decomposition used here,
  although the controls below are team-scoped, not organisation-scoped.
- [Google's AI Principles](https://ai.google/responsibility/principles/) —
  the principle set used in §4. See the editorial note in
  §1 of this solution about keeping the wording current across
  Google's periodic revisions.
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework) —
  the structural anchor the team uses to *decide* which artifacts
  to cite in each row (mod-110 ex-01 does this translation).
- [NIST AI 600-1 Generative AI Profile](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf) —
  the specific risk-category vocabulary used in the
  prompt-injection, confabulation, and IP rows.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) —
  cited in the privacy/security rows as the taxonomy for the
  prompt-injection and adversarial-input review items.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat
  taxonomy for the security rows.
```

## 3. Validation steps

To validate a completed mapping before publishing it:

1. **Every row names a specific artifact.** "The team's processes" is
   not an artifact; `docs/INCIDENT_RUNBOOK.md` is. Rework any row
   whose artifact column is vague.
2. **Every row names an enforcement event.** Policy that lives only
   in a document, with no review, eval, or ritual that fires on
   violation, is a commitment with no gate. Mark it pending.
3. **Every row points at evidence.** If someone asks "show me a case
   where this control fired," the row must say where that case is
   recorded.
4. **Rows marked pending have an owner and a target.** Pending rows
   without a target are hidden gaps.
5. **Both principle sets are present with native wording.**
   Paraphrasing either set to make them line up is a common error;
   keep the original and merge in §5.
6. **The merge view has exactly one team control per principle
   overlap.** If the map asks the team to maintain two fairness
   controls (one Microsoft-labelled, one Google-labelled), it has
   failed. The point of the merge view is deduplication.
7. **Off-cycle triggers include "the other company revised".**
   Microsoft and Google update their principles; the map must
   survive that.
8. **The tone is "here is how we route," not "here is how virtuous
   we are."** Any phrase in the mapping that reads like marketing
   copy should be deleted.

## 4. Rubric

Grade a completed mapping. 0–3 each. Pass bar: **16 / 24**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| P1 | **Fidelity to source wording** | Paraphrased principles | Microsoft's or Google's wording preserved | Both preserved | Both preserved + source links + last-revision-date tracked |
| P2 | **Artifact specificity** | "Processes" | Artifact named per row | Named per row + path in repo | Named + path + file exists today |
| P3 | **Enforcement event per row** | None | Some | All rows | All rows + the event is one a peer could attend |
| P4 | **Evidence per row** | None | Some | All rows | All rows + evidence is diff-reviewable |
| P5 | **Deduplication via merge view** | Parallel controls per principle set | Some overlap acknowledged | Merge view present | Merge view + exactly one control per overlap + traceable to both source principles |
| P6 | **Visible gaps** | Omitted | Mentioned in prose | Table of pending rows | Table + owner + target + why-not-yet |
| P7 | **Review triggers** | None | Quarterly only | Quarterly + enumerated off-cycle triggers | Triggers include source-company revisions and are automatable where possible (RSS/announcement feed on the frontmatter) |
| P8 | **Tone** | Reads as marketing | Mixed | Factual and evidence-first | Factual + humble about pending rows + explicit disclaimer that mapping ≠ compliance |

## 5. Common mistakes

- **Treating principles as vibes instead of controls.** Writing
  "we value fairness" under the Fairness header is not a mapping. The
  mapping is the file, the gate, and the log row that proves the
  claim.
- **Maintaining duplicate controls for overlapping principles.**
  Microsoft's "Reliability and safety" and Google's "Safety" are the
  same control. Two controls doubles cost without doubling coverage
  and guarantees the two will drift. Build one, link twice.
- **Hiding gaps.** A map that claims a control for every principle
  when half the controls do not exist is worse than a map with an
  honest "commitment held; control pending" table. Reviewers notice
  the former; they can plan around the latter.
- **Mapping to the aspirational version of the team's policy.**
  Link the file that exists today, not the one you plan to write next
  quarter. A map is a snapshot of practice, not a plan.
- **Letting the mapping drift from the source.** Microsoft and
  Google both revise. If the map is still citing the 2022 wording of
  Microsoft's Responsible AI Standard in 2027, it is stale by
  reference. Put the off-cycle trigger in the frontmatter.
- **Writing marketing in the map.** "The team is committed to the
  highest standards of..." sentences come from brochures, not
  routing docs. Delete them; use a specific artifact reference
  instead.
- **Letting the principles crowd out specific laws.** Microsoft RAI
  and Google AI Principles are not laws. If the team ships into the
  EU or into regulated sectors, the regulations (EU AI Act, sectoral
  rules) take precedence. The map should name that precedence in its
  "What this is not" section.
- **Forgetting the editorial-review clause.** Google in particular
  has revised its principles; the team should check the live page
  rather than reciting the version memorised years ago.

## 6. References

- [Microsoft Responsible AI](https://www.microsoft.com/en-us/ai/responsible-ai) —
  the authoritative public source for Microsoft's Responsible AI
  principles (Fairness; Reliability and safety; Privacy and
  security; Inclusiveness; Transparency; Accountability) and the
  Responsible AI Standard (v2, 2022) that decomposes them into
  goals and controls at organisational scope.
- [Google's AI Principles](https://ai.google/responsibility/principles/) —
  the authoritative public source for the Google AI Principles used
  in §4. These principles have been revised; the mapping document's
  off-cycle trigger list includes re-checking this page.
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) —
  provides the artifact-level decomposition discipline that the
  mapping relies on; mod-110 ex-01 is the companion translation.
- [NIST AI 600-1: Generative AI Profile](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf) —
  risk-category vocabulary for the specific controls in the
  privacy/security rows.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) —
  the risk taxonomy for the prompt-injection and adversarial-input
  rows.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat
  taxonomy used in the security rows.

*Editorial note:* the principle labels used in §4 are based on the
long-standing engineering interpretation of Google's AI Principles,
but Google has reorganised the public principle page between
revisions; confirm the current wording at
ai.google/responsibility/principles/ at the time the team publishes
its mapping, and update §4 wording and §7 revision-date tracking
accordingly.
