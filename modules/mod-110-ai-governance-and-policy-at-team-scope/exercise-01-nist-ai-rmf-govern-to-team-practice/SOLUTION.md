# Exercise 01 — NIST AI RMF GOVERN Function Translated to Team Practice

> Module: `mod-110-ai-governance-and-policy-at-team-scope`
> Exercise type: **design** (worked translation + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is a two-column *translation table* plus a short prose
rationale. The left column is the GOVERN function of the **NIST AI Risk
Management Framework (AI RMF 1.0)** at the category / subcategory level
and its Generative AI supplement, **NIST AI 600-1**. The right column is
what a team manager **actually does on Monday morning** to meet that
subcategory for their team — the artifact they write, the ritual they
run, the review they attend, the trailing evidence they keep.

NIST AI RMF is written for *organisations*. A team manager cannot pass
an enterprise audit on their own; they can, however, show how their
team's day-to-day practice contributes to each of the six GOVERN
categories. That contribution is what this exercise makes concrete.

The translation is **down**, not **sideways**:

- The org-level claim is: *"The organisation has policies in place to
  address AI risks."* (GOVERN 1.1)
- The sideways rewrite is: *"Our team has an AI policy document."* —
  which is a restatement, not a translation.
- The down-translation is: *"Our team has `docs/AI_POLICY.md` in the
  repo, revised quarterly on a named date by a named owner, with the
  last revision diff reviewable by any IC; the policy is named in
  onboarding, surfaced in `AGENTS.md`, and enforced by the PR template
  and MCP allow-list."*

Design decisions this solution encodes:

1. **Translate per subcategory, not per category.** NIST's categories
   are too broad to be actionable; subcategories are where the control
   actually lives. GOVERN 1 has eight subcategories; collapsing them
   loses the useful distinctions (policy *exists* vs. policy *is
   reviewed* vs. policy *is auditable*).
2. **Name an artifact for each row.** If the right column cannot point
   at a file, a repo, a Jira board, or a calendar invite, the row is
   aspiration, not practice. A rubric criterion enforces this.
3. **Name an owner — a person, not a role.** "The team" owning
   something is nobody owning it. Row owners become the people a skip
   or auditor can go to with one question.
4. **Name a review cadence.** NIST AI RMF is explicit that governance
   is *continuous*, not one-shot. A row without a cadence is a row
   that will be stale within two quarters.
5. **Use the Generative AI profile where it tightens an abstract
   row.** AI 600-1 provides risk-category language (confabulation,
   data privacy, information integrity, dangerous or violent content,
   value chain and component integration, information security,
   intellectual property, obscene/degrading content, environmental,
   human-AI configuration, harmful bias/homogenisation) that is more
   specific than the base RMF for the kinds of AI assistants the team
   is actually using. Where the generic row is vague, the AI 600-1 row
   is pulled in and named.
6. **Don't translate rows that are not the team manager's.** Some
   GOVERN subcategories (board oversight, enterprise-wide risk
   tolerance) are not a team-scope deliverable. The translation
   explicitly marks these *"up-escalated"* and records who owns them
   — rather than pretending the team can satisfy them alone.

## 2. Implementation — worked translation table

### 2.1 GOVERN 1 — Policies, processes, procedures, and practices

| NIST AI RMF subcategory (abridged) | Team-manager translation | Named artifact | Named owner | Cadence |
|---|---|---|---|---|
| **GOVERN 1.1** — Legal and regulatory requirements involving AI are understood, managed, and documented. | The team maintains a one-page register of the AI-related regulations that *apply to this team's work* (e.g., EU AI Act Article 4 literacy obligations if any user is in the EU; sectoral rules relevant to the team's domain). Legal is consulted on the register, not consulted *per decision*. | `docs/AI_REGS_REGISTER.md` | Team manager, with named legal liaison | Quarterly review; off-cycle when a named regulation is amended |
| **GOVERN 1.2** — The characteristics of trustworthy AI are integrated into organizational policies. | The team's `AGENTS.md` and `AI_POLICY.md` explicitly name the trustworthy-AI characteristics the team scopes to (valid and reliable; safe; secure and resilient; accountable and transparent; explainable and interpretable; privacy-enhanced; fair with harmful bias managed) and how each is enforced in review (PR checklist items, eval gates). | `docs/AI_POLICY.md` with a `## Trustworthy-AI characteristics` section | Team manager | Quarterly |
| **GOVERN 1.3** — Processes, procedures, and practices are in place to determine the needed level of risk management. | The team has a documented *tiering* for AI-touching changes: low-risk (text help, tests), medium-risk (production code behind feature flag), high-risk (customer-facing content generation, decisions). Tier determines review depth, eval coverage, and HITL gate. | `docs/AI_RISK_TIERS.md` (referenced from PR template) | Team manager | Reviewed after every post-mortem that touches a mis-tiered change |
| **GOVERN 1.4** — The risk management process and its outcomes are established through transparent policies, procedures, and other controls. | Every AI-adjacent decision the team makes is recorded in a dated ADR under `docs/adr/`. Governance decisions are not oral. | `docs/adr/` (markdown ADRs, numbered) | ADR author; team manager archives | Per-decision |
| **GOVERN 1.5** — Ongoing monitoring and periodic review of the risk management process and its outcomes. | The team runs a **governance review ritual** once per quarter: open ADR list, open evaluation failures, open incidents, open policy drift findings. One hour, calendar invite, notes in `docs/governance-reviews/`. | `docs/governance-reviews/<YYYY-QN>.md` | Team manager | Quarterly, on a calendar-fixed date |
| **GOVERN 1.6** — Mechanisms are in place to inventory AI systems and are resourced according to organizational risk priorities. | The team maintains `docs/AI_INVENTORY.md`: every AI assistant, every MCP server, every model endpoint, every evaluation suite, with named owner and last-review date. The inventory is linked from the governance review ritual. | `docs/AI_INVENTORY.md` | Team manager | Monthly diff review; full re-review quarterly |
| **GOVERN 1.7** — Processes and procedures are in place for decommissioning and phasing out AI systems safely. | When a model, prompt, or agent is retired, there is a dated `docs/decommissions/<id>.md` note capturing: what was removed, what replaced it, what historical outputs are still live and how long they will be retained, which evaluations the replacement passed. Decommission is not just deletion. | `docs/decommissions/` | Owner of the retired system; team manager archives | Per-decommission |

### 2.2 GOVERN 2 — Accountability structures

| Subcategory (abridged) | Team-manager translation | Named artifact | Named owner | Cadence |
|---|---|---|---|---|
| **GOVERN 2.1** — Roles, responsibilities, and lines of communication related to mapping, measuring, and managing AI risks are documented and clear. | The team's RACI for AI work is in the repo. For each class of change — code assistant use, retrieval index updates, agent configuration, eval design, incident response — the Responsible / Accountable / Consulted / Informed people are named. "The team" is not a valid cell value. | `docs/AI_RACI.md` | Team manager | Quarterly, and when a role changes |
| **GOVERN 2.2** — The organization's personnel and partners receive AI risk management training to enable them to perform their duties and responsibilities. | Every IC on the team completes a *written* AI literacy acknowledgement during onboarding (see mod-110 exercise-02), covering: the team's AI policy, the data classes that may and may not go into AI tools, the review standards, the incident process, and the specific risks of the AI assistants the team uses. Renewed annually. | `docs/onboarding/ai-literacy-checklist.md`; completions tracked in `docs/onboarding/completions.md` | Team manager | Annual re-affirm |
| **GOVERN 2.3** — Executive leadership of the organization takes responsibility for decisions about risks associated with AI system development and deployment. | **Up-escalated.** Team manager documents the escalation path: which decisions go to engineering director, which go to CTO, which go to the enterprise AI governance body (if one exists). Team-scope artifact is the *escalation table*, not the decision itself. | `docs/AI_ESCALATION.md` | Team manager | Revised when the org chart changes |

### 2.3 GOVERN 3 — Workforce diversity, equity, inclusion, and accessibility

| Subcategory (abridged) | Team-manager translation | Named artifact | Named owner | Cadence |
|---|---|---|---|---|
| **GOVERN 3.1** — Decision-making related to mapping, measuring, and managing AI risks throughout the lifecycle is informed by a diverse team. | AI-policy decisions, eval design, and incident post-mortems involve at least two roles (e.g., IC + manager, or IC + reviewer from another team) and are not authored by a single person in the dark. For teams of two or fewer, a named cross-team reviewer is designated. | `docs/AI_POLICY.md` §Review contributors (names + roles); ADR header lists reviewers | Team manager | Per-decision |
| **GOVERN 3.2** — Policies and procedures are in place to define and differentiate roles and responsibilities for human-AI configurations and oversight of AI systems. | For every agent / assistant the team runs, the policy names: (a) who may invoke it, (b) who may change its configuration, (c) who reviews its outputs, (d) who is paged if it misbehaves. These four answers may not all be the same person. | `AGENTS.md` and `docs/MCP_POLICY.md` (from mod-106 exercise-03) | Team manager | Quarterly, cross-ref with GOVERN 1.6 inventory |

### 2.4 GOVERN 4 — Culture of risk management

| Subcategory (abridged) | Team-manager translation | Named artifact | Named owner | Cadence |
|---|---|---|---|---|
| **GOVERN 4.1** — Organizational policies and practices foster a critical thinking and safety-first mindset. | The team's PR template includes explicit AI-review items (does the author explain the diff; does every imported identifier exist; is any generated content attributed; see mod-103 ex-01). PRs cannot be merged without these items addressed. | `.github/pull_request_template.md` | Team manager | Reviewed after every post-mortem; hard-blocked by CI linter for the "author can explain" item where feasible |
| **GOVERN 4.2** — Organizational teams document the risks and impacts of their AI technology and communicate about the impacts more broadly. | Each team publishes an **AI practice brief** to a wider engineering audience once per half: what AI tools are in use, what evaluation coverage exists, what incidents happened, what the team changed. The brief is written for engineering peers, not executives. | `docs/ai-practice-briefs/<YYYY-H1 or H2>.md` | Team manager | Half-yearly |
| **GOVERN 4.3** — Organizational practices are in place to enable AI testing, identification of incidents, and information sharing. | The team's **incident runbook** (mod-109 ex-01) includes AI-specific signal routing: prompt-injection suspicion → secops; model-output hallucination at user scale → on-call + comms; eval regression → auto-filed Linear issue. | `docs/INCIDENT_RUNBOOK.md` with `## AI-specific signals` section | Team manager | Reviewed after every AI-adjacent incident |

### 2.5 GOVERN 5 — Engagement with AI actors

| Subcategory (abridged) | Team-manager translation | Named artifact | Named owner | Cadence |
|---|---|---|---|---|
| **GOVERN 5.1** — Organizational policies and practices are in place to collect, consider, prioritize, and integrate feedback from those external to the team that developed or deployed the AI system regarding the potential individual and societal impacts related to AI risks. | The team runs a **feedback inbox** for AI-produced outputs that reach users or other teams: a Slack channel, a labelled Linear queue, or a form. Items are triaged weekly and feed into either the evaluation suite or the policy. | `docs/AI_FEEDBACK_INTAKE.md` describing the inbox mechanics + triage; `#ai-feedback-<team>` channel or equivalent | Team manager + named triager | Weekly triage; monthly rollup |
| **GOVERN 5.2** — Mechanisms are established to enable AI actors to regularly incorporate adjudicated feedback from relevant AI actors into system design and implementation. | Adjudicated feedback (from GOVERN 5.1) that implies an eval gap generates a new eval fixture; feedback that implies a policy gap generates an ADR; feedback that implies a training gap generates an onboarding update. Nothing adjudicated is dropped on the floor. | `docs/ai-feedback-log.md` with disposition column (`→ eval`, `→ ADR`, `→ onboarding`, `→ wontfix with reason`) | Named triager | Per-item |

### 2.6 GOVERN 6 — Third-party AI risks

| Subcategory (abridged) | Team-manager translation | Named artifact | Named owner | Cadence |
|---|---|---|---|---|
| **GOVERN 6.1** — Policies and procedures are in place to address AI risks and benefits arising from third-party software and data. | Every third-party AI tool (model API, assistant, MCP server, retrieval index source) is listed in the team's AI inventory (GOVERN 1.6) **with a link to its vendor trust / data-handling statement** and a last-reviewed date. New third-party tools enter via a short admission review, not by an IC installing them locally. | `docs/AI_INVENTORY.md` + `docs/tool-admissions/<tool>.md` | Team manager | Per-admission; re-review at vendor TOS changes |
| **GOVERN 6.2** — Contingency processes are in place to handle failures or incidents in third-party data or AI systems deemed to be high-risk. | For every third-party AI tool, the team names a **fallback path** (manual, alternative model, internal cache, or "stop using the feature") and documents the condition under which the fallback is activated. Fallback is tested at least once a year. | `docs/AI_CONTINGENCY.md` with one row per high-risk third-party dependency | Team manager | Annual drill |

### 2.7 Generative AI profile (NIST AI 600-1) deltas worth naming

NIST AI 600-1 adds risk categories specific to generative AI. A team using a code assistant or an LLM-backed feature in product should also carry these additions as rows in the team policy — they're where the base RMF is thinnest:

- **Confabulation** — the team maintains a documented policy that
  generated content presented to users is grounded (retrieval,
  citation, or human-authored) OR explicitly marked as unverified.
  Enforced in review.
- **Data privacy** — the team's data-handling policy
  (exercise-04) names which classes of data may enter which AI tools.
- **Information security** — prompt-injection is part of the incident
  runbook and part of code review for retrieval pipelines.
- **Intellectual property** — generated code is reviewed against the
  team's IP policy; attribution rules are explicit.
- **Human-AI configuration** — see GOVERN 3.2 above (roles are
  documented per-agent).
- **Information integrity** — any user-facing generated content has a
  named reviewer gate before publication (mod-106 ex-04 HITL design).
- **Value chain and component integration** — see GOVERN 6.1 above.

Teams that produce or deploy user-facing generative output should add
one row per AI 600-1 risk category to `AI_POLICY.md`.

## 3. Validation steps

To validate a completed translation table before taking it upward:

1. **Every row has a named artifact.** Not "we'll write one" — an
   actual path that exists in the repo. Walk through the table and
   verify. A row without an artifact is unmet.
2. **Every artifact has a named owner who is a person, not a role.**
   "Team manager" is acceptable because that is one person in this
   team; "the team" is not acceptable because it is zero people.
3. **Every row has a cadence.** "Ongoing" is not a cadence. "Quarterly
   on the second Monday of the quarter" is.
4. **Up-escalated rows are labelled.** Rows the team cannot satisfy
   alone are not hidden; they are marked and the escalation target is
   named. A translation that claims the team satisfies every GOVERN
   subcategory is not credible.
5. **AI 600-1 deltas are addressed if the team ships generative
   output.** If yes and the deltas are not in the policy, the
   translation is incomplete.
6. **Cross-references to other module artifacts are live.** The
   translation should name — and link to — the PR template, incident
   runbook, delegation contract, MCP policy, data-handling policy.
   Governance is a web; a translation that treats it as a pillar is
   missing the point.
7. **A new IC could operate the team on this document alone for a
   week.** If they could not — if there is tribal knowledge not
   captured — the translation is not done yet.

## 4. Rubric

Grade a completed translation on the eight criteria below. 0–3 each.
Pass bar: **16 / 24**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| G1 | **Subcategory coverage** | Only categories, not subcategories | Subcategories present but several missing | All GOVERN subcategories addressed | All GOVERN subcategories + relevant AI 600-1 deltas |
| G2 | **Artifact specificity** | "A document somewhere" | Named artifact for most rows | Named artifact for every row | Named artifact + path in the repo + exists today |
| G3 | **Owner specificity** | Owners are roles or "the team" | Owners named for most rows | Named person for every row | Named person + named backup for high-risk rows |
| G4 | **Cadence** | None stated | Vague ("regularly") | Concrete cadence per row | Concrete cadence + calendar-fixed date + trigger for off-cycle review |
| G5 | **Honest up-escalation** | Claims to satisfy enterprise-scope rows alone | Some rows labelled up-escalated | Clear escalation table | Clear escalation table + named destination + last-synced date |
| G6 | **Cross-module coherence** | Treats this translation as a standalone document | References one or two other modules | References the PR template, incident runbook, delegation contract, MCP policy, data policy | Cross-references are hyperlinks in the repo, not prose claims |
| G7 | **Reproducibility** | Only this manager could have written this | A peer manager could approximate it | A peer could run the same translation protocol | Protocol is explicit (which subcategories to skip, how to pick artifacts) so a peer can translate against *their* team |
| G8 | **AI 600-1 awareness** | No mention | Mention of generative AI | ≥ 3 AI 600-1 risk categories addressed | All AI 600-1 categories relevant to the team's output class are addressed, with distinct controls |

## 5. Common mistakes

- **Copy-pasting the NIST subcategory language.** Restating the
  framework is not translating it. A reader who already knows the
  framework learns nothing; a reader who does not learns no more. The
  right column must be new content, not a rephrasing.
- **Writing "the team has a culture of safety" as if that were a
  control.** Culture is an outcome, not a translation. The translation
  of GOVERN 4.1 is the PR template item that fires if the author
  cannot explain the diff, not an assertion about ethos.
- **Owning every row.** A team manager who claims to own board-level
  AI risk oversight has misread what their team's scope is.
  Up-escalation is a strength, not an admission.
- **Translating once, then never again.** A translation that is not on
  the quarterly governance review calendar will be stale within two
  quarters; the subcategories are static, but the artifacts, owners,
  and tools are not.
- **Pretending AI 600-1 does not apply.** Teams using any LLM-based
  assistant are using generative AI; the AI 600-1 deltas apply. The
  common dodge is "we're just using a code assistant, not a
  user-facing generative feature." Confabulation, IP, and
  information-security risks do not care about the distinction.
- **Writing the translation in a wiki page the agent cannot see.**
  Governance artifacts the agent cannot read are governance artifacts
  the agent will not follow. Put them in the repo, in markdown, under
  version control.
- **Merging GOVERN with MAP / MEASURE / MANAGE.** The other three
  functions are addressed in later modules (eval commissioning,
  productivity measurement, incident response). Pulling them into the
  GOVERN translation makes it unreadable and makes the quarterly
  review impossible to scope.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) —
  the authoritative source for the GOVERN function and its
  subcategories; `AI RMF 1.0` document (January 2023) and the
  companion `AI RMF Playbook` provide the subcategory numbering
  (GOVERN 1.1–1.7, 2.1–2.3, 3.1–3.2, 4.1–4.3, 5.1–5.2, 6.1–6.2)
  used as the left column of the translation table above.
- [NIST AI 600-1: Artificial Intelligence Risk Management Framework —
  Generative AI Profile](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf) —
  provides the generative-AI-specific risk categories
  (confabulation; data privacy; environmental; harmful bias and
  homogenization; human-AI configuration; information integrity;
  information security; intellectual property; obscene, degrading,
  and/or abusive content; value chain and component integration;
  dangerous, violent, or hateful content) used to extend the generic
  GOVERN rows in §2.7.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) —
  referenced here because the team's trustworthy-AI "secure and
  resilient" row (GOVERN 1.2) ties to specific OWASP ML risk
  categories in the PR template and incident runbook.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat
  taxonomy for AI systems; referenced in GOVERN 4.3's incident
  signal-routing row so AI-specific incidents are classified against
  a shared vocabulary rather than invented locally.
