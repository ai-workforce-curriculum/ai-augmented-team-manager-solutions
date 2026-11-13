# Exercise 05 — Data Handling, DPA, and SOC 2 Posture Review

> Module: `mod-111-vendor-budget-and-tool-selection-at-team-scope`
> Exercise type: **design** (DPA & SOC 2 review protocol + worked packet + rubric)

## 1. Solution overview

The deliverable is a **data-posture review packet**: a line-by-line read
of an AI vendor's Data Processing Agreement (DPA) and its SOC 2 Type II
report, cross-referenced against the team's data-and-IP handling policy
(Mod-110 Ex-04). The packet is the artifact that lets the team manager,
with their legal and security partners, answer one question before
signature: *does this vendor's written posture actually say what we need
it to say about our data, and where exactly is the clause?*

Scope. This exercise is the depth read behind exercise-04's broader
vendor packet. Where exercise-04 records *that* a DPA and SOC 2 report
exist and *that* they were reviewed, this exercise is the actual review.
The output flows back into exercise-04 as evidence for the D-group
(data handling) and B-group (security posture) lines.

The review covers two artifacts and one cross-reference:

- The **DPA**: a legally binding document. For teams in EU-regulated or
  US-regulated data, it is the controlling contract on *what the vendor
  may do with the team's data*. Governing frameworks include GDPR
  Article 28 (processor obligations), Article 32 (security of
  processing), and sector rules where applicable.
- The **SOC 2 Type II report**: an audit attestation covering a defined
  period. It describes *how* the vendor meets the AICPA Trust Services
  Criteria (TSC) — security, availability, processing integrity,
  confidentiality, and privacy — over the audit window, and reports
  exceptions. Type II means controls were tested over time; Type I is a
  point-in-time design review and is not a substitute.
- The **cross-reference**: how the DPA's commitments and the SOC 2's
  tested controls map to the team's data-and-IP handling policy. A
  commitment that does not map to a control, and a control that does
  not map to a commitment, are each findings.

A good packet is:

- **Clause-anchored.** Every finding cites the DPA §X.Y or the SOC 2
  control ID (e.g. CC6.1). "The vendor says yes" is not a citation.
- **Scoped to the team's data posture.** A packet written to a generic
  bar is theatre. The packet reviews against *this team's* policy
  obligations, and names what is not relevant.
- **Honest about gaps.** The SOC 2 report has exceptions. The DPA has
  silences. The packet lists them explicitly instead of hiding them in
  "mostly pass."
- **Dated and bounded.** DPAs change with amendments; SOC 2 reports
  cover a specific audit period. The packet records both dates and
  names what is still valid.
- **Reviewer-named.** Legal and data-privacy partners sign the packet;
  the team manager cannot sign it alone.
- **Actionable.** The packet's output is a short list of accepted /
  mitigated / blocking findings, with named owners and target dates.

Design decisions this solution encodes:

1. **DPA is read against a defined checklist, not freestyle.** A clause
   that is not present in a DPA is a clause the vendor is not committed
   to; silence is not consent and silence is not safety.
2. **SOC 2 Type I is not equivalent to Type II for live production use.**
   Type I is design-only; the packet notes this and demands Type II for
   V1 vendors.
3. **Scope first, controls second.** A SOC 2 report scoped to the
   vendor's marketing website is not a report on the product. The packet
   checks the in-scope system boundary before scoring any control.
4. **Exceptions are the interesting part.** The reviewer reads the
   exceptions section first; a clean report is rare, and the question is
   whether the exceptions are material to the team's use.
5. **CUECs are a to-do list for the team.** A SOC 2 report ships a list
   of complementary user-entity controls — things the team must do to
   realize the control's benefit. The packet extracts these into an
   owned to-do list.
6. **Bridge letters matter.** If the audit window ends more than 90 days
   before today, the packet requires a bridge letter or does not accept
   the report.
7. **Training-data posture is a DPA kill-switch, not a marketing claim.**
   The packet must find the clause. If the only "no training" statement
   is on a sales page, the finding is blocking.
8. **The packet's output drives exercise-04 and feeds back into the
   team's internal policy.** It is not a standalone artifact.

## 2. Implementation — the worked review packet

### 2.1 Inputs and ownership

Before the review:

- **DPA**, signed or offered for signature. Must be the actual proposed
  version, not an earlier revision.
- **SOC 2 Type II report**, retrieved from the vendor's trust center or
  under NDA. Must include: system description, control matrix, auditor's
  opinion, exceptions section, subservice-organization table, and (if
  applicable) a bridge letter.
- **Team data-and-IP handling policy** from Mod-110 Ex-04.
- **Reviewers named:** legal partner (DPA), security partner (SOC 2),
  team manager (cross-reference + sign-off).
- **Retrieval dates** for both artifacts recorded in the packet header.

### 2.2 DPA review protocol

Each DPA is read against the checklist in §2.3. Mark each clause
**Present / Mitigated / Missing** with the specific section cited. The
protocol is:

1. Read the definitions block. Confirm that "Personal Data," "Customer
   Data," "Prompts and Outputs," and any AI-specific term the vendor
   uses are defined in a way consistent with how the team will use the
   service. Vague or absent definitions are findings.
2. Confirm the **controller / processor allocation** matches reality. In
   most "the team uses the vendor's SaaS" cases, the team is the
   controller and the vendor is the processor; if the vendor claims
   "joint controller" for any purpose, flag it.
3. Walk the checklist (§2.3) clause by clause. Record the exact
   section number and a one-sentence paraphrase. Do not paraphrase in a
   way that softens a clause.
4. Cross-reference against the team's data-and-IP policy. Any clause
   that softens a team policy commitment is a finding; any clause that
   is silent where the policy requires a commitment is a finding.
5. Produce the DPA findings list. This feeds exercise-04 D-group.

### 2.3 DPA checklist (filled against a fictional vendor "FooAI")

| # | Clause | Status | Cite | Notes |
|---|---|---|---|---|
| D1 | Purpose limitation: vendor processes team data only to provide the service | Present | §3.1 | "Solely for the purpose of providing and supporting the Services." |
| D2 | Training-data opt-out: prompts, outputs, embeddings, traces not used to train vendor's models on the team's tier | Present | §4.2 | Explicit; covers "inputs, outputs, embeddings, and derived data." Covers all tiers. |
| D3 | Retention: a zero- or short-retention tier is contractually offered and the team is on it; retention schedule documented | Present | §5.1 | 24-hour retention tier selected on order form; references Schedule B. |
| D4 | Data residency: processing confined to agreed regions | Present | §6.1 | US-only processing; references sub-processor list §7. |
| D5 | Sub-processors: list published; change-notification SLA | Present | §7.3 | 30 days' written notice of new sub-processors; right to object. |
| D6 | Cross-border transfer mechanism (where applicable): SCCs, DPF, or adequacy basis | Present | §6.3 | EU SCCs executed as Schedule C; transfer impact assessment on file. |
| D7 | Security measures: Article 32-aligned technical and organisational measures | Present | §8 + Schedule D | Schedule D maps to Article 32; cross-referenced to SOC 2 CC6. |
| D8 | Breach notification: ≤ 72h SLA, defined "security incident" scope | Present | §9.1 | 72 hours; incident scope includes "any unauthorised access, disclosure, loss, or alteration." |
| D9 | Data return and deletion on termination | Present | §10.2 | Return within 30 days of termination; deletion within 60 days; legal-hold exceptions enumerated. |
| D10 | Audit rights (or trust-report equivalent with cadence) | Mitigated | §11 | Trust-report cadence only; on-site audit by prior arrangement. Accepted with finding. |
| D11 | Confidentiality of data covers personnel and sub-processors | Present | §12 | Flow-down obligation to sub-processors. |
| D12 | Vendor employee access controls, including role-based access and training | Present | §8.4 | Plus annual security training referenced. |
| D13 | Use of de-identified or aggregated data (if any) is scoped and lawful | Mitigated | §4.5 | Vendor retains the right to produce de-identified aggregate telemetry (volume, feature-use) for product improvement. Not training. Documented as "aggregate telemetry only, no inversion risk" — finding records this and sets a re-review on any scope change. |
| D14 | Vendor assistance with DSARs / data-subject requests | Present | §13 | 30-day response SLA; team contact responsible. |
| D15 | Liability for data breach: cap and carve-outs for willful misconduct | Present | MSA §11 | Reviewed by legal partner; within policy. |
| D16 | Order-of-precedence clause clarifying DPA > generic MSA on data | Present | §1.4 | Confirmed. |
| D17 | AI-specific: no third-party model provider added that would change the processing surface without notice | Present | §7.4 (addendum) | Flow-down to inference sub-processors with notification. |
| D18 | AI-specific: team's prompts and outputs are the team's data, not vendor's | Present | §3.3 | Ownership of inputs and outputs rests with the team. |

Missing or weakly-worded clauses would be captured here even if there
are many. Presentation matters: a missing D2 is a kill-switch, a missing
D15 is a legal redline but not operational.

### 2.4 SOC 2 Type II review protocol

Each SOC 2 Type II report is read as:

1. **Confirm the report type.** Type II, covering an audit period of at
   least 3 months (6–12 typical). Type I is design-only and does not
   count for V1 vendors.
2. **Note the audit period.** If the period ended > 90 days before
   today, require a bridge letter covering today's date. No bridge
   letter = report not accepted.
3. **Identify the auditor and confirm they are a licensed CPA firm.**
   The auditor's opinion is only as credible as the auditor.
4. **Read the system description.** Confirm the "in-scope system"
   actually includes the product the team is buying — not just the
   corporate website. A SOC 2 that scopes out the AI inference stack is
   a SOC 2 that does not cover what the team cares about.
5. **Review the Trust Services Criteria selected.** Security (common
   criteria CC-series) is universal; Availability (A-series),
   Confidentiality (C-series), Processing Integrity (PI-series), and
   Privacy (P-series) are optional additions. For an AI vendor
   processing team code or data, Security + Confidentiality are the
   minimum; Availability is operationally important; Privacy is
   required if the vendor processes personal data.
6. **Read the exceptions section.** This is the interesting part; it is
   where controls failed during the period and the vendor's response is
   recorded.
7. **Pull the Complementary User Entity Controls (CUECs).** The report
   lists things the team must do to realize the controls' benefit; these
   are the team's to-do list.
8. **Review the Subservice Organizations.** SOC 2 reports commonly
   exclude certain sub-processors ("subservice organizations") from
   scope. Confirm they are either covered by their own SOC 2 or
   acknowledged as excluded; follow up on any that the team cares
   about operationally.
9. **Reconcile scope against the DPA.** Controls mapped to Article 32
   measures in the DPA must have corresponding evidence in the SOC 2 or
   be flagged.

### 2.5 SOC 2 checklist (filled against the same fictional vendor)

| # | Line | Status | Cite |
|---|---|---|---|
| S1 | Report type is Type II | Pass | Cover; "SOC 2 Type II"; §I. |
| S2 | Audit period is ≥ 3 months and ended ≤ 90 days ago, or bridge letter is in hand | Pass | Period 2026-04-01 → 2026-09-30; bridge letter dated 2026-11-01 covering through 2026-10-31. |
| S3 | Auditor is a licensed CPA firm | Pass | <Auditor name>, license verified. |
| S4 | In-scope system covers the product we buy | Pass | §II "System Description" names the inference product and the hosted agent runtime. |
| S5 | TSC: Security (CC) selected | Pass | Mandatory. |
| S6 | TSC: Availability (A) selected | Pass | Appropriate for live-service dependency. |
| S7 | TSC: Confidentiality (C) selected | Pass | Appropriate for team-code processing. |
| S8 | TSC: Privacy (P) selected (if processing personal data) | Pass | Selected. |
| S9 | Auditor's opinion unqualified | Pass | §III. |
| S10 | Exceptions section reviewed | Pass with findings | §V; see §2.6 of this packet. |
| S11 | CUECs identified and assigned to a team owner | Pass | 6 CUECs; owners assigned in §2.7. |
| S12 | Subservice organizations identified and covered | Mitigated | 3 sub-services carved out; 2 have their own SOC 2 shared; 1 (a monitoring provider) does not. Finding filed. |
| S13 | Report maps to DPA security measures | Pass | Cross-reference table §2.8 of this packet. |
| S14 | Controls relevant to AI-specific concerns present (training-data isolation, prompt-logging retention, content-filter controls) | Mitigated | CC6.6 covers tenant isolation; CC6.7 covers logical access; no AI-specific control named — "AI SDLC" lives under CC7. Not a blocker but a watch item. |
| S15 | No material misstatements flagged by auditor | Pass | §III. |

### 2.6 Exceptions — the detail

Reviewed from §V of the report.

| # | Exception | Impact | Status |
|---|---|---|---|
| E-1 | Control CC4.1 (information about internal control is communicated to internal parties): one quarterly compliance newsletter not sent within SLA. | Low. Internal communications only; no customer impact. | Accepted. |
| E-2 | Control CC6.8 (unauthorized software prevented): two laptops in the test window had an endpoint-protection agent out of compliance for < 7 days; remediated within SLA. | Low. Not vendor production. | Accepted. |
| E-3 | Control CC7.3 (incidents evaluated for impact): one incident evaluated after the 48h SLA (actual: 61h). | Medium. Impacts incident-response posture. | Mitigated; vendor's remediation plan reviewed and acceptable. Track for next cycle. |

### 2.7 CUECs — the team's to-do list

The SOC 2 report ships six Complementary User Entity Controls the team
must implement for the vendor's controls to be effective.

| # | CUEC | Team owner | Status | Evidence |
|---|---|---|---|---|
| U-1 | Enforce MFA on tenant admin accounts | team manager | Done | Google Admin audit, 2026-11-11. |
| U-2 | Review team roles and remove ex-employees within 24h | team manager | Done | Offboarding runbook, Mod-109 Ex-02. |
| U-3 | Rotate API keys / tokens on the published schedule | security partner | Done | Secret manager rotation policy. |
| U-4 | Promptly report suspected security incidents to the vendor | team manager | Done | Incident runbook §4; link to vendor's `security@`. |
| U-5 | Configure retention / tier settings on the tenant per policy | team manager | Done | Admin console screenshot 2026-11-10. |
| U-6 | Educate users on acceptable-use restrictions | team manager | Done | Enablement program Mod-105 Ex-01. |

CUECs without a done status are blocking findings and keep the packet
open.

### 2.8 DPA × SOC 2 × Policy cross-reference

A compact mapping to confirm that each team-policy obligation ties to
both a DPA clause and a SOC 2 control. Missing cells are findings.

| Team policy (Mod-110 Ex-04) | DPA clause | SOC 2 control |
|---|---|---|
| No training on team data | D2 (§4.2) | n/a — contractual-only commitment, not a tested control |
| Short retention | D3 (§5.1) | CC5.3 (data classification + retention), CC6.1 (logical access) |
| Access control & RBAC inside the vendor | D12 (§8.4) | CC6.1, CC6.3 |
| Tenant isolation | D7 (§8 + Schedule D) | CC6.6 |
| Incident response 72h | D8 (§9.1) | CC7.3, CC7.4 — see exception E-3 |
| Deletion on termination | D9 (§10.2) | CC5.3 |
| Sub-processor flow-down | D5 (§7.3), D11 (§12) | n/a in the vendor's own report; subservice carve-out — see S12 |
| Encryption in transit and at rest | D7 (Schedule D) | CC6.7 |
| Audit log export | D7 (Schedule D) | CC7.2 |
| Data residency US-only | D4 (§6.1) | CC6.6 (via tenant allocation) |
| Prompt / output ownership | D18 (§3.3) | n/a — contractual-only |

### 2.9 Findings produced by the packet

These feed exercise-04 and the signing decision.

| # | Finding | Severity | Owner | Target |
|---|---|---|---|---|
| F-1 | D10 (§11): no formal customer audit right; mitigated with trust-report cadence. | Medium | legal partner | Renewal cycle |
| F-2 | D13 (§4.5): vendor retains the right to produce de-identified aggregate telemetry for product improvement. Non-training; accepted with re-review trigger if scope changes. | Low | team manager | Trigger-only |
| F-3 | S12: one subservice organization (monitoring provider) does not share a SOC 2. | Medium | security partner | 2027-01-15; request SOC 2 or alternative attestation. |
| F-4 | S14 / cross-reference §2.8: "no training on team data" and "prompt / output ownership" are contractual-only commitments. Track for a future vendor-side control (e.g. technical isolation attestation). | Medium (watch) | team manager | Renewal cycle |
| F-5 | E-3: one incident evaluated after 48h SLA (actual 61h); vendor remediation accepted; re-check next SOC 2 cycle. | Low | security partner | Next SOC 2 review |
| F-6 | No AI-specific control family in the SOC 2. Not blocking; monitor industry practice and re-raise next cycle. | Low (watch) | team manager | Next SOC 2 review |

No blocking findings; packet is **conditionally signed** pending closure
of F-3 by 2027-01-15.

### 2.10 Sign-off block

Signed by:
- Legal partner: <name>, 2026-11-12 (DPA sections).
- Security partner: <name>, 2026-11-12 (SOC 2 sections).
- Team manager: <name>, 2026-11-13 (cross-reference and overall).

The packet is not valid without all three signatures.

### 2.11 Validity window

- **Valid through:** earlier of (i) the next SOC 2 report release;
  (ii) 2027-10-31 (12 months from retrieval); (iii) any DPA amendment.
- **Triggered re-review on:** new sub-processor on the no-fly list; a
  vendor-side incident with customer impact; a material change to
  retention or training-opt-out language; a change in the team's data
  classification policy.

## 3. Validation steps

Before sign-off:

1. **Did you read the DPA against your team's policy**, not against a
   generic checklist? A generic review passes because the DPA passes
   itself; the only useful review is the one against the policy.
2. **Is every clause citation the actual section number**, not a
   paraphrase? "Vendor commits to 72h breach notice" without a §X.Y is
   not a review; it is a repetition.
3. **Did you confirm the SOC 2 is Type II**, with an audit period ≥ 3
   months, ended within 90 days or covered by a bridge letter? If not,
   the report is not accepted; flag and do not sign.
4. **Did you confirm the in-scope system is the product the team is
   buying**, not the corporate marketing site?
5. **Did you read the exceptions section?** A SOC 2 is interesting
   because of its exceptions; a reviewer who skips to the opinion page
   has not reviewed.
6. **Did you list the CUECs and assign a team owner to each?** The team
   is responsible for them; the SOC 2 is contingent on them being done.
7. **Did you check the subservice organizations?** A SOC 2 with a
   carved-out subservice the team cares about is a report with a
   missing chapter.
8. **Did you cross-reference DPA, SOC 2, and team policy?** A commitment
   in the DPA with no corresponding control in the SOC 2 is a
   contractual-only commitment; the packet names it as such.
9. **Did you avoid "pending evidence" as a resting place?** For any clause
   you could not find, write `<!-- pending-evidence: ... -->` and *follow
   up* before signing. Don't sign on silences.
10. **Did you record retrieval dates for both artifacts?** A review
    against a prior-version DPA that the vendor has since amended is
    not a review of the current state.

## 4. Rubric

Use this to grade a submitted review packet. Each criterion scores 0–3.
Pass bar: **20 / 30**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Both artifacts retrieved; dates recorded** | One or none | Both retrieved, no dates | Both + dates | Both + dates + validity window + re-review triggers |
| R2 | **DPA checklist filled with clause citations** | No citations | Some | Most clauses | Every clause present / mitigated / missing with §X.Y |
| R3 | **SOC 2: report type and period validated** | Not checked | Type confirmed | Type + period | Type + period + bridge letter + auditor verification |
| R4 | **SOC 2: in-scope system matches product** | Not checked | Mentioned | Confirmed | Confirmed with the system-description §II paraphrased |
| R5 | **Exceptions section read and summarised** | Not read | Skimmed | Summarised | Summarised + impact-assessed per exception + status set |
| R6 | **CUECs extracted and owned** | Not extracted | Listed | Owned | Owned + evidence per CUEC + any gaps flagged as blocking |
| R7 | **Subservice organizations assessed** | Not assessed | Noted | Assessed | Assessed + follow-up items filed for carve-outs |
| R8 | **Cross-reference DPA × SOC 2 × policy** | Absent | Partial | Present | Present + contractual-only commitments flagged |
| R9 | **Findings list produced** | Absent | Prose | List | List + severity + owner + target date + blocking status |
| R10 | **Sign-off block complete** | Absent | One reviewer | Two | All three (legal / security / team manager) + validity window |

## 5. Common mistakes

- **Reading the DPA for comprehension rather than commitment.** Prose
  that sounds reassuring can be legally non-committal. Only clauses
  that create obligations count; the review looks for obligations, not
  for reassurance.
- **Accepting SOC 2 Type I as equivalent to Type II.** Type I describes
  design on a single date; Type II demonstrates operation over a
  period. For live production use, Type II is required.
- **Ignoring the audit period.** A SOC 2 whose period ended a year ago
  with no bridge letter is a historical document, not a current
  assurance.
- **Accepting a scoped-out system description.** A SOC 2 whose in-scope
  system is the vendor's corporate website, not the AI product, is
  irrelevant to the team's risk.
- **Skipping the exceptions.** A clean SOC 2 is rare; exceptions are
  normal; the question is whether the exceptions are material to the
  team's use. A reviewer who skips the exceptions section has not
  reviewed the report.
- **Not listing the CUECs.** The report's assurances are conditional on
  the team doing its part; CUECs are that part. Un-owned CUECs are
  blocking findings.
- **Ignoring subservice carve-outs.** "This report does not cover
  <monitoring-vendor>" is a signal to go find <monitoring-vendor>'s
  attestation, not to pretend the control chain is complete.
- **Confusing "no training" on the home page with a DPA clause.**
  Marketing commitments are not contractual; D2 must be a DPA clause
  by section.
- **Accepting "de-identified aggregate telemetry" without a definition
  and a scope.** Aggregate telemetry is often fine; aggregate *outputs*
  are not. The packet should pin down scope and set a re-review
  trigger on changes.
- **No cross-reference.** A DPA commitment with no SOC 2 control is a
  contractual-only commitment; a control with no DPA commitment is
  tested-but-not-promised. Both are useful to the team; mixing them is
  not.
- **Silent findings.** The packet's value is in the findings list, not
  in the long checklist. Hiding findings in the body of the review
  defeats the point.
- **Signing without a validity window and triggers.** A packet that
  never expires is a packet that stops matching reality on the first
  DPA amendment.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework)
  — the **Govern** function's third-party-risk expectations are the
  direct structural anchor for this packet (govern-6.1, govern-6.2).
  The **Measure** function's demand for *analysable, traceable* evidence
  underwrites the clause-and-control-citation discipline — every
  finding ties to §X.Y of the DPA or a specific SOC 2 control ID. The
  **Manage** function's attention to residual risk is why the packet's
  outputs are classified blocking / mitigated / accepted with named
  owners and dates rather than a single pass/fail.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/)
  — the supply-chain-attack category is why the packet pulls on
  subservice organizations (S12) rather than treating the vendor as a
  single opaque boundary. The data-poisoning / model-extraction
  categories motivate the DPA's training-opt-out depth (D2) and the
  attention to prompt/output ownership (D18).
- [MITRE ATLAS](https://atlas.mitre.org/) — the adversarial-threat
  taxonomy for ML systems. Referenced to sanity-check that the packet's
  cross-reference captures the data-handling threats most relevant to
  AI vendors (indirect prompt injection via vendor features, tenancy
  isolation failures, training-pipeline exposure), rather than
  reviewing only to a generic SaaS threat model.

<!-- follow-up: once the completeness pipeline has shipped a second
cycle of module solutions, add a worked re-review across a DPA
amendment, showing how an amended retention clause triggers a packet
re-run and feeds back into exercise-04's D-group. -->
