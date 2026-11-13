# Exercise 05 — Certifications Portfolio Planner

> Module: `mod-112-cross-functional-leadership-and-ritual-calendar`
> Exercise type: **design** (worked portfolio plan, decision rubric, grading rubric)
>
> **Note on exercise prompt.** The learning-side prompt for this
> exercise is scaffolded as TBD. This solution derives the exercise
> shape directly from [Chapter 6 — Certifications as Cross-Functional
> Vocabulary](https://github.com/ai-workforce-curriculum/ai-augmented-team-manager-learning)
> (`06-certifications-as-cross-functional-vocabulary.md`). When the
> exercise prompt fills in, this solution should be reconciled with
> any specifics it adds; the artifacts below should remain valid
> because they reflect the chapter's named discipline.

## 1. Solution overview

The deliverable is **v0.1 of the manager's certifications portfolio
plan**: a narrow, dated plan naming which AI-adjacent certifications
the manager will pursue, in what order, under what organisational
constraints, and what operational behaviour each credential is
expected to change on the Chapter 3 decision-rights grid, the Chapter 4
pre-read, the Chapter 5 one-pager, and the cross-team forums the
manager participates in.

Certifications at L40 are **cross-functional vocabulary, not
authority**. They lower the translation cost of partnership
conversations with counsel, the governance peer, privacy office,
Platform / DevEx, and Data. They do not confer scope that mod-110
assigned elsewhere.

The solution encodes five design choices the portfolio will be graded
against:

1. **Primary anchor: [IAPP AIGP](https://iapp.org/certify/aigp/).**
   The default for L40 managers running AI-augmented work. Governance
   vocabulary; composes cleanly with `mod-110` practice;
   non-vendor-specific. The portfolio starts here unless the primary
   partner surface is clearly not governance.
2. **Secondary anchor: [ISO/IEC 42001 lead-implementer](https://www.iso.org/standard/81230.html).**
   Load-bearing only when the organisation has committed to an AIMS
   certification and the team's evidence feeds that program. Heavier
   than AIGP.
3. **Portfolio is narrow.** Typically AIGP + one or two situational
   credentials maximum. A portfolio of six credentials signals
   credentialism rather than operational competence.
4. **Every credential has to pay rent.** A credential that would not
   change behaviour on the grid, in the pre-read, in the one-pager, or
   in cross-team forums is a credential the portfolio does not need.
5. **Continuing-education cost is counted.** AIGP = 20 CPEs / 2 years;
   CIPP similar; ISO 42001 varies. A two-credential portfolio ≈ 40
   hours/year of CPE; a four-credential portfolio ≈ 80+ hours. The
   portfolio is sized to the role, not maxed out.

## 2. Implementation — worked plan for manager at the "Underwriting-assist team" (fictional calibration)

Composite context used across the mod-112 solutions. An L40 engineering
team manager, 9-person team, consumer-credit (Americas), with the five
named partners from Exercise 02. The organisation has these
credentialing-relevant facts:

- **Privacy maturity.** Counsel and the DPO hold CIPP/E; privacy is a
  mature function with weekly cross-functional touchpoints.
- **AIMS commitment.** The organisation announced in its Q3 board
  update the intent to pursue ISO/IEC 42001 certification over the
  next 18 months.
- **Jurisdictional exposure.** EU and US footprint; EU AI Act Article
  52 transparency obligations apply to the team's agent-assist
  features; GDPR Article 22 applies to automated-decision-adjacent
  outputs.
- **Framework posture.** The organisation runs Scrum/Kanban hybrid at
  team level; no org-wide SAFe commitment.
- **Cloud posture.** Multi-cloud (AWS primary for inference, GCP for
  analytics); no single-cloud dominance.

### 2.1 Primary partner's shared language — the first portfolio question

Per Chapter 6, the first portfolio question is: *which credential is
my primary partner's most common shared language?*

Observed partnership load this quarter (from Exercise 02 grid-row
activity + weekly ritual attendance):

| Partner | Weekly load | Shared-language signal |
|---|---|---|
| AI-Governance peer (S. Mwangi) | 2–3 touchpoints/wk + quarterly packet | References NIST AI RMF sub-categories by name (GOVERN 1.3, MANAGE 2.4); references Article 14/50 by number; AIGP body-of-knowledge concepts land cleanly |
| Counsel (via DPO) | ~1/wk during Article 50 workflow drafting | CIPP/E vocabulary; AIGP compatibility strong |
| Product (J. Smith) | 3–4 touchpoints/wk | Product vocabulary — no certification shared |
| Data (M. Patel) | 2/wk | Metric-definition vocabulary, statistical validity — no certification shared |
| Design (R. Okafor) | 1/wk | Design-systems vocabulary — no certification shared |
| Platform EM (L. Chen) | 2/wk | Developer-productivity vocabulary (DX Core 4, SPACE) — no formal certification |

Verdict: the primary partner surface where certification is the
shared language is the AI-Governance peer + counsel. AIGP is the
primary anchor.

### 2.2 Load-bearing-in-organisation — the second portfolio question

Per Chapter 6, the second question is: *which credential is load-bearing
in my team's organisation and sector?*

- **AIMS commitment is active.** Every team's evidence will feed the
  AIMS over the next 18 months. ISO/IEC 42001 lead-implementer is
  load-bearing for the manager whose team's evidence feeds it.
- **EU AI Act exposure is current.** AIGP covers the Act's major
  articles; adequate for the manager's day-to-day partnership load.
- **GDPR exposure is high but held by counsel/DPO.** CIPP/E is
  held by counsel; manager's job is to *route* to them, not to
  re-decide privacy triggers. Manager does not need CIPP/E.

### 2.3 Opportunity-cost — the third portfolio question

Per Chapter 6, the third question is: *what is the opportunity cost?*
CPE hours accumulate; a maxed-out portfolio is credentialism.

Credential CPE load (approximate, from accrediting-body published
requirements at time of writing — verify at exam booking):

| Credential | CPE / year | Notes |
|---|---|---|
| IAPP AIGP | ~10 | 20 CPEs / 2 yrs |
| CIPP/E | ~10 | Similar cycle |
| ISO/IEC 42001 lead-implementer | ~10–15 | Varies by accreditor; see ISO 17024 list |
| PMP | ~20 | 60 PDUs / 3 yrs |
| AWS / Azure / GCP AI | ~10 | Varies |
| SAFe (Agilist, PO/PM, etc.) | ~10 | Annual re-cert |

Target: two active credentials beyond any previously-held. 20–25
hours/year CPE combined. Sustainable alongside team-management load.

### 2.4 The portfolio decision table

| Credential | Decision | Timeline | Reasoning |
|---|---|---|---|
| **IAPP AIGP** | **Pursue** | Within 6 months (2027-05) | Primary partner surface (Governance peer + counsel) use AIGP body-of-knowledge concepts weekly. Composes with `mod-110` practice; partner conversations run cleaner with shared vocabulary. Non-vendor-specific. |
| **ISO/IEC 42001 lead-implementer** | **Pursue** | Within 12 months (2027-11) | Organisation has committed to AIMS pursuit; team's governance evidence feeds the AIMS program. Lead-implementer credential lets the manager shape evidence to compose with AIMS clauses rather than translate at ingest. |
| **CIPP/E** | Defer | — | Counsel + DPO hold it; manager's grid role is to *route* privacy triggers to them, not to re-decide. AIGP + `mod-110` practice covers the privacy-adjacent vocabulary the manager needs at L40. Revisit if primary partner surface shifts toward privacy. |
| **Cloud AI (AWS / Azure / GCP)** | **Decline** | — | Multi-cloud; no single-cloud dominance. Cloud-specific credentials are platform-team territory (Platform EM). The manager's cross-functional conversations with Platform do not benefit from a cloud-vendor credential. |
| **PMP** | **Decline** | — | No org-wide PMI commitment; `mod-102` + `mod-106` cover the manager's roadmap and workflow fluency without framework certification. CPE cost (~20 hours/year) is high for no partnership-vocabulary return. |
| **SAFe Agilist** | **Decline** | — | No org-wide SAFe commitment. Scrum/Kanban hybrid is handled at team level without framework certification. |
| **CISSP / ISO 27001 Lead Implementer** | **Decline** | — | SecOps peer holds the security credential; manager's role is to engage SecOps through the engineered-risk relationship (`mod-109` Ch. 5), not to replicate their credential. Over-reach at L40. |
| **CompTIA DataX / SAS cert.** | **Decline** | — | Data partner (M. Patel) holds the data credentials. Manager engages on metric-definition vocabulary learned in `mod-104`; no need for a parallel data cert. |
| **DASA DevEx coach** | **Decline** | — | Not in a DevEx-adjacent role; Platform EM holds the DevEx vocabulary. |

Active portfolio after 12 months: **AIGP (new) + ISO/IEC 42001
lead-implementer (new)**. Plus whatever previously-held framework
credential the manager carried from prior roles. Target total CPE
load: ~25 hours/year. Sustainable.

### 2.5 Behaviour-change expectations — rent each credential will pay

Chapter 6 is explicit: credentials earn their place by changing
specific behaviours. The plan names which behaviour each credential
will change.

**AIGP (by Q3 2027).**

- **Chapter 3 decision-rights grid.** The "workflow risk
  classification" row and the "policy-triggered workflow amendment"
  row currently route to the risk engineer by default because the
  manager lacks the vocabulary to participate on equal footing. After
  AIGP, the manager Recommends classification with specific GOVERN
  sub-category citations rather than passing through.
- **Chapter 4 pre-read.** The governance section currently uses
  generic framing ("one gap opened, routed upward"). After AIGP, the
  section cites NIST AI RMF sub-categories by name and references
  Article 14 / 50 by number, matching the vocabulary of the architect
  who consumes the pre-read.
- **Chapter 5 one-pager.** The governance-posture line currently
  paraphrases; after AIGP, it names the framework mapping.
- **Cross-team forums.** The engineering-managers' guild and the
  cross-team governance forum both reference AIGP concepts weekly.
  After AIGP, the manager can report; before, they present in
  paraphrase.

**ISO/IEC 42001 lead-implementer (by Q1 2028).**

- **Governance packet shape.** Current packet is organised around
  `mod-110` crosswalk categories; after lead-implementer, the packet
  is shaped to compose directly with AIMS Annex A controls,
  eliminating the AIMS-ingest translation step.
- **Chapter 4 pre-read.** The governance section cites AIMS clause
  numbers where material changes land (Clauses 6.1, 8.3, 9.1 as
  typical anchors), speaking the architect's language in the register
  of the AIMS program.
- **Decision log.** Entries that touch AIMS-relevant decisions
  (policy amendments, workflow classification, incident handling)
  cite the AIMS clause they engage, giving the architect direct
  traceability.

### 2.6 Timeline, costs, and milestones

| Milestone | Date | Notes |
|---|---|---|
| Decision-log entry: v0.1 portfolio plan | 2026-11-13 | This plan recorded as a decision-log entry (see §2.7) |
| AIGP study begins | 2026-11-20 | 8–10 weeks of study at ~5 hr/wk |
| AIGP exam scheduled | 2027-02 | Target pass within one attempt; retake window ≥60 days if needed |
| AIGP behavior-change assessment (mid) | 2027-05 | Does the governance section of Q1 pre-read use AIGP vocabulary? |
| AIGP behavior-change assessment (full) | 2027-08 | Does the Governance-partnership grid show post-AIGP changes? |
| ISO/IEC 42001 training begins | 2027-07 | 40-hour training block; accredited by PECB or equivalent (verify against ISO 17024 list at booking) |
| ISO/IEC 42001 exam | 2027-10 | — |
| ISO/IEC 42001 behavior-change assessment | 2028-02 | Does the governance packet shape map to AIMS clauses? |

**Total time cost:**

- AIGP: ~50 hours study + exam day + ~10 hours CPE/year.
- ISO 42001 lead-implementer: ~40 hours training + exam + ~15
  hours CPE/year.
- Year 1: ~75 hours. Year 2+: ~25 hours CPE.

**Direct financial cost (approximate; verify at booking):**

- AIGP: exam fee + training. Low 4-figures.
- ISO 42001: training + exam. Mid 4-figures.

Both costs cleared with the manager's own manager (DL-2026-11-13-00X)
as part of the Q1 professional-development budget.

### 2.7 Decision-log entry

```markdown
# DL-2026-11-13-010 — v0.1 certifications portfolio plan

Date: 2026-11-13
Author: <team manager>
Status: Active
Scope: Manager's professional-development plan

## Context

Chapter 6 names certifications as cross-functional vocabulary at L40.
The team's primary cross-functional partner on credentialed vocabulary
is the AI-Governance peer and counsel (AIGP body-of-knowledge
concepts). The organisation has committed to AIMS certification over
18 months; the team's governance evidence feeds the AIMS program.

## Decision

v0.1 certifications portfolio: pursue AIGP within 6 months and
ISO/IEC 42001 lead-implementer within 12 months. Decline CIPP/E,
cloud-AI certs, PMP, SAFe, CISSP, data-cert, DevEx coach for the
reasons recorded in the portfolio table. Active portfolio after 12
months: AIGP + ISO 42001 lead-implementer, plus any prior-held
framework credential.

## Rationale

Primary-partner shared-language test: AIGP wins. Load-bearing-in-org
test: AIMS commitment makes ISO/IEC 42001 load-bearing. Opportunity-cost
test: AIGP + ISO 42001 together ≈ 25 CPE hr/year, sustainable alongside
team management. Each credential has a behaviour-change expectation
on the grid, the pre-read, the one-pager, and cross-team forums; see
§2.5 of the portfolio plan.

## Alternatives considered

- AIGP only, defer ISO 42001 — rejected: AIMS commitment is active;
  evidence shaping compounds; one year of AIMS work without the
  credential wastes compositional opportunity.
- AIGP + CIPP/E — rejected: CIPP/E vocabulary is held by counsel /
  DPO; manager's job is to route, not re-decide. AIGP + `mod-110`
  practice covers the privacy-adjacent vocabulary the manager needs.
- All three (AIGP + CIPP/E + ISO 42001) — rejected: CPE load > 40
  hr/year with no additional partnership vocabulary buy. Credentialism.
- No credentials, lean on `mod-110` practice — rejected: the primary
  partner surface references AIGP concepts weekly; practice without
  credential-shared vocabulary leaves the manager presenting in
  paraphrase.

## Reversal conditions

- If the AIMS commitment is rescinded or deprioritised by the
  organisation, defer ISO/IEC 42001 and reconsider the portfolio.
- If the primary partner surface shifts (e.g., a new counsel partner
  who uses a different privacy framework; a new governance peer who
  does not reference AIGP), re-run the shared-language question.
- If CPE load exceeds 40 hr/year without corresponding partnership
  vocabulary return, drop the weakest-performing credential.

## Review date

2027-05-13 (post-AIGP exam). Full portfolio review at 2027-11-13.

## Links

- Chapter 6 portfolio-planning rubric
- Exercise 02 Governance-partnership grid (the primary place AIGP
  pays rent)
- AIMS program announcement (Q3 2026 board update)
- Manager's own manager Slack thread approving development budget
```

## 3. Validation steps

Before finalising the portfolio plan and closing the exercise:

1. **Does the plan answer Chapter 6's three questions in order?**
   Primary-partner shared language; load-bearing in organisation;
   opportunity cost. Answered sequentially.
2. **Is the portfolio narrow?** Typically AIGP + one or two
   situational credentials. A portfolio of four+ is credentialism;
   re-run the opportunity-cost question.
3. **Does every credential name the behaviour it will change?** Chapter
   3 grid row, Chapter 4 pre-read section, Chapter 5 one-pager line, or
   a cross-team forum. A credential with no behaviour-change is a
   credential the portfolio does not need.
4. **Are declined credentials explained, not ignored?** The declines
   are the discipline of the portfolio: they show which temptations
   the plan refused.
5. **Is CPE load computed and compared to the manager's available
   time?** The sustainability of the portfolio is a function of
   ongoing CPE cost, not just initial certification effort.
6. **Is the plan dated and in the decision log?** Portfolio decisions
   drift; the review date and reversal conditions are in the entry.
7. **Does the plan respect the ownership boundary?** No credential is
   pursued to claim scope that `mod-110` assigned to the governance
   architect or program head.
8. **Is the manager's own manager informed and budget cleared?**
   Financial cost + development-time cost are visible.
9. **Are behaviour-change assessments scheduled?** Mid-cycle and
   full-cycle checks on whether the credential is actually paying
   rent.

## 4. Rubric

Grade a submitted portfolio plan. Each criterion 0–3. Pass bar:
**16 / 24**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Three questions answered in order** | Not answered | Answered implicitly | Answered explicitly | Answered + evidence per answer |
| R2 | **Primary anchor named and justified** | No primary | Primary named without justification | AIGP (or alternative) named with primary-partner evidence | AIGP named + weekly-touchpoint evidence + behavior-change expectation |
| R3 | **Secondary anchor handled** | Omitted | ISO 42001 named but not conditioned | ISO 42001 pursued or declined with organisation-level reason | Pursued or declined with explicit AIMS-commitment evidence and reversal condition |
| R4 | **Portfolio is narrow** | > 4 credentials | 4 credentials | 2–3 credentials | 1–2 credentials with explicit declines of the rest |
| R5 | **Every credential pays rent** | Behavior-change not named | Named generically | Named per credential | Named per credential with grid row / pre-read section / one-pager line / forum citation |
| R6 | **Declined credentials explained** | Ignored | Named but no reason | Reason per decline | Reason per decline + reversal condition for revisiting |
| R7 | **CPE load computed** | Absent | Rough estimate | Per-credential CPE named | Per-credential + total + comparison to available time |
| R8 | **Decision-log entry + behaviour-change assessment cadence** | No log entry | Log entry without reversal conditions | Log entry + review date | Log entry + reversal conditions + mid-cycle and full-cycle assessments scheduled |

## 5. Common mistakes

- **Portfolio-as-resume.** Pursuing credentials for the LinkedIn
  profile rather than for operational behaviour change. The portfolio
  grows; team practice does not.
- **Certifications-as-authority.** Pursuing AIGP and then reaching
  into governance-architect scope. The credential is vocabulary, not
  authority. Ownership boundaries from `mod-110` still hold.
- **Ignoring the credential market.** The opposite failure: declining
  all credentials and finding the manager presenting in paraphrase
  when partners use AIGP concepts. Vocabulary matters; the manager
  without it pays full price in every partnership conversation.
- **Pursuing all three cloud credentials.** Flattens the signal;
  nobody is impressed; CPE load triples. Pick zero or one.
- **Pursuing CIPP/E when counsel holds it.** Replicates the counsel
  credential without adding partnership vocabulary at the level the
  manager works at. Manager's role is to route, not to re-decide
  privacy triggers.
- **Pursuing ISO 42001 without an AIMS commitment.** Possible, but
  the credential is 40+ hours of training with lower vocabulary
  return. Better served by deeper `mod-110` practice.
- **No behaviour-change assessment.** A credential pursued without a
  mid-cycle and full-cycle check on whether it is paying rent is a
  credential that will persist on the portfolio past its usefulness.
- **CPE math skipped.** Four credentials × 10–20 CPE/year each quickly
  hits 60+ hours/year — the equivalent of one full work-week of
  continuing education. Compute the number.
- **Budget not cleared.** Development time and credential fees are
  real costs; a portfolio plan that assumes the manager will pay for
  it personally or will absorb study time out of weekend hours is not
  sustainable.
- **No reversal conditions.** The AIMS commitment could be rescinded;
  the primary partner could change. The portfolio plan is a decision
  that can be revisited; name the triggers.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework)
  — the framework is both what AIGP covers in its body of knowledge
  and what the manager's team-scope practice (`mod-110` Ch. 2)
  implements. AIGP formalises the vocabulary; NIST AI RMF is the
  substantive content underneath. The **Govern** function's call for
  *roles with documented competencies* is the structural reason
  certifications are treated as vocabulary rather than theatre — the
  competency has to be observable in behaviour change, not just in
  certificate ownership.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/)
  — categories like training-data poisoning, model inversion, and
  training-serving skew are what the AI-Governance peer's grid rows
  are defending. A manager with AIGP vocabulary engages those
  categories on equal footing; without, the manager hands off. The
  portfolio plan explicitly names this as a behaviour-change the
  AIGP credential buys.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat
  taxonomy. ATLAS categories are the shared vocabulary between the
  governance peer, the architect, and (upstream) the head of
  governance. AIGP covers framework-level governance; ATLAS covers
  adversarial threat classification — both are vocabulary the
  portfolio plan is explicitly choosing *not* to replicate at
  credential level (ATLAS has no formal certification exam), relying
  instead on practice-level fluency from `mod-109` and `mod-110`.

<!-- follow-up: when exercise-05's learning-side prompt fills in,
revise this solution to reconcile with any specifics it adds —
particularly if the prompt names a different worked-example context
(e.g., a team at an organisation *without* an AIMS commitment, which
would flip the ISO/IEC 42001 decision), or specifies a credential
market anchor other than the AIGP default in Chapter 6. -->
