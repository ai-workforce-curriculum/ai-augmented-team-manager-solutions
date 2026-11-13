# Exercise 04 — Vendor Due Diligence Checklist

> Module: `mod-111-vendor-budget-and-tool-selection-at-team-scope`
> Exercise type: **design** (checklist + worked packet + grading rubric)

## 1. Solution overview

The deliverable is a **vendor due diligence packet**: a filled checklist
plus the evidence that supports each line, produced before the team
signs a contract with any AI vendor. The packet answers one question for
the team manager's skip, for legal, and for security: *has this vendor
cleared the bar our team needs it to clear, and where exactly is the
evidence?*

The packet is the artifact that stops the team from discovering, on the
first production incident, that "we thought they had SOC 2" was a
rumour, or that the vendor's sub-processors include one the team's
data-handling policy forbids.

Scope. The checklist applies to any AI vendor the team proposes to
install: an IDE copilot, a hosted agent runtime, an inference provider,
an observability or eval platform, a third-party MCP server author,
a model marketplace. It does **not** cover the deeper data-and-contract
review that is exercise-05's job — that exercise reads the DPA and the
SOC 2 report line by line. This exercise is the broader surface-area
sweep.

A good packet is:

- **Evidence-anchored.** Every "pass" is backed by a document, a URL,
  a timestamp, a contact, or a filed artifact. "Our sales rep said yes"
  is not evidence.
- **Scored at a tier appropriate to blast radius.** A vendor whose
  product touches customer data is held to a higher bar than one that
  will only ever see team internal code; the checklist is tiered.
- **Owned and dated.** The packet names a human reviewer per section
  (manager, security, legal) and the date of evidence retrieval.
- **Findings-oriented, not form-oriented.** The output is a short list
  of accepted / mitigated / blocking findings, not a 100-line "mostly
  pass" spreadsheet.
- **Reusable across vendors.** The same checklist is run against any
  vendor; vendor-specific nuance goes into the evidence column.
- **Dated and revisited.** The packet is valid for 12 months or until a
  material change, whichever is sooner.

Design decisions this solution encodes:

1. **Tier the vendor first; score against that tier.** A model provider
   that will see team code gets the full checklist. An MCP tool that
   reads a read-only replica gets a slimmed one. Applying the same bar
   to a $5/mo plug-in and a $50k/yr inference contract wastes the team's
   time and loses the signal.
2. **Separate "corporate" risk from "product" risk.** A well-run vendor
   with a bad product posture can still ship an incident on the team;
   a product with great posture from a vendor about to be acquired is a
   different problem. Both are in scope.
3. **Data handling is a kill-switch group.** As in exercise-01, any
   failure in the data-handling block disqualifies the vendor regardless
   of elsewhere. Exercise-05 drills into this block; the packet records
   its pass/fail outcome.
4. **The packet names "no-fly" sub-processors.** The team's data-and-IP
   handling policy (Mod-110 Ex-04) names providers the team will not
   ship data to transitively. The packet confirms the vendor does not.
5. **Exit and portability are first-class items.** How does the team
   leave this vendor if the relationship ends? Is the data portable?
   Is the tenancy deletable? Will the vendor cooperate? "Yes" needs an
   evidence citation, not a sales assertion.
6. **The reviewer per section is named.** The team manager owns
   operational sections; security owns security sections; legal owns
   contractual sections. A packet where every section says "the team
   manager said yes" is a packet where nothing was actually reviewed.
7. **Open findings land in a tracker, not in the packet.** The packet
   closes with a tracker link, not with resolutions invented on the fly.

## 2. Implementation — the worked packet

### 2.1 Vendor tiering (do this first)

Assign a vendor tier before scoring. The tier determines which sections
of the checklist are required vs. optional.

| Tier | Definition | Required sections |
|---|---|---|
| **V1 Critical** | Vendor sees prompts, completions, source code, customer data, or produces changes applied to team systems (IDE copilot, hosted agent runtime, inference provider, hosted eval platform). | All (§2.3 A–I). |
| **V2 Standard** | Vendor sees team internal metadata or logs but not source or customer data (observability, telemetry, cost analytics). | A, B, C, D, F, G, H. |
| **V3 Peripheral** | Vendor sees nothing team-owned beyond public metadata; is itself open-source and self-hosted. | A, B (lightweight), D (self-host posture). |

Record the assigned tier at the top of the packet and the reason for the
tier assignment in one sentence.

### 2.2 Section ownership

Every section names a required reviewer:

| Section | Reviewer |
|---|---|
| A. Corporate | team manager |
| B. Security posture | security partner |
| C. Product security | security partner + team manager |
| D. Data handling | data/privacy partner (deep dive in Ex-05) |
| E. Model / AI safety | team manager + security partner |
| F. Operations & SLA | team manager |
| G. Incident response | team manager + security partner |
| H. Legal & contractual | legal partner |
| I. Exit / portability | team manager |

Where the team does not have the role on staff, name the external
reviewer (fractional counsel, security consultant) explicitly.

### 2.3 Checklist (filled against a fictional vendor "FooAI" — V1)

Each line scores **Pass / Mitigated / Fail / N/A** with evidence.
Templates below are kept short so the full packet fits on 3–4 pages;
the evidence attachments are linked, not inlined.

#### A. Corporate

| # | Line | Status | Evidence |
|---|---|---|---|
| A1 | Legal entity and jurisdiction known | Pass | Delaware C-corp; `doc://foo/corp/certificate`. |
| A2 | Funding runway > 12 months or revenue > break-even | Mitigated | Vendor provides written statement of runway; we accept with a quarterly re-check. `doc://foo/corp/runway-2026-10`. |
| A3 | No undisclosed material litigation | Pass | Vendor letter dated 2026-10-28. |
| A4 | Ownership / control changes in last 12 months disclosed | Pass | None reported. |
| A5 | Public contact for security, press, legal | Pass | `security@foo.ai`, `press@foo.ai`, `legal@foo.ai`. |
| A6 | Insurance (cyber, E&O) in force with named limits | Pass | Certificate on file; carrier confirmed. |

#### B. Security posture

| # | Line | Status | Evidence |
|---|---|---|---|
| B1 | SOC 2 Type II report ≤ 12 months old | Pass | Report retrieved 2026-11-07; see Ex-05 for line-item review. |
| B2 | ISO 27001 or equivalent (if required by team) | N/A | Not required for this tier. |
| B3 | Independent penetration test ≤ 12 months old | Pass | Executive summary retrieved; full report under NDA. |
| B4 | Bug bounty or responsible-disclosure program | Pass | `security.txt` confirmed; public program link. |
| B5 | Published CVE / security-advisory history | Pass | 2 advisories in last 18 months; both resolved within SLA. |
| B6 | MFA / SSO available on tenant admin | Pass | SAML / OIDC; MFA enforceable. |
| B7 | Audit log export available | Pass | S3 / JSON export; retention ≥ 1 year. |
| B8 | Encryption in transit (TLS ≥ 1.2) and at rest | Pass | Documented; verified in pentest summary. |
| B9 | Role-based access control inside the vendor | Pass | Documented in trust center. |
| B10 | Vendor is not on the team's deny list (sanctions, policy) | Pass | Checked against policy list; no hits. |

#### C. Product security

| # | Line | Status | Evidence |
|---|---|---|---|
| C1 | Threat model published or shared under NDA | Pass | Shared under NDA 2026-11-05; summarised in packet attachment. |
| C2 | Secure SDLC claims backed by audit evidence (not marketing) | Pass | SOC 2 CC7 section; dev-process attestation. |
| C3 | Dependency / SBOM available | Pass | SBOM for the product shared 2026-11-02; reviewed by security partner. |
| C4 | Container / image provenance (signed releases) | Mitigated | Images signed; signing root rotation policy not yet documented. Finding filed, see §2.4. |
| C5 | Auth model documented and resistant to token leakage | Pass | OAuth flows documented; short-lived tokens; rotation supported. |
| C6 | Prompt-injection awareness in product design | Pass | Docs describe mitigations; aligns with MITRE ATLAS tactics on indirect prompt injection; see Ex-05 attachment. |
| C7 | Secrets never required in plaintext in requests | Pass | Documented; no API shape requires inline secrets. |
| C8 | Self-serve data export of team content | Pass | Export API; format documented. |

#### D. Data handling (kill-switch group — full depth in Ex-05)

| # | Line | Status | Evidence |
|---|---|---|---|
| D1 | DPA signed, scoped to the tier we buy | Pass | Signed DPA 2026-11-10. |
| D2 | Contractual training-data opt-out on our tier | Pass | DPA §4.2; confirmed in writing by vendor counsel. |
| D3 | Zero- or short-retention tier available and selected | Pass | 24h retention tier selected; DPA §5.1. |
| D4 | Data residency matches our policy | Pass | US-only processing; DPA §6.1. |
| D5 | Sub-processor list published and change-notified | Pass | List retrieved 2026-11-10; 30-day change notice in DPA §7.3. |
| D6 | No "no-fly" sub-processors | Pass | Reviewed against team data-and-IP policy (Mod-110 Ex-04). |
| D7 | Breach notification obligation ≤ 72h | Pass | DPA §9.1. |
| D8 | Data deletion on offboarding; retention schedule documented | Pass | DPA §8.2; verified 30-day deletion + legal-hold carve-out. |
| D9 | Regulatory in-scope (GDPR, CCPA, sector-specific) | Pass | DPA Schedule A; GDPR + CCPA addenda executed. |

Fail or Mitigated on any D-line is a kill-switch for V1 vendors unless
mitigation is contractually binding and evidenced. Do not advance.

#### E. Model / AI safety

| # | Line | Status | Evidence |
|---|---|---|---|
| E1 | Model-provenance / training-data posture disclosed to the extent the vendor can | Pass | Training-data statement published; scoped claims. |
| E2 | Red-team / safety-evaluation program described | Pass | Public safety-evaluations page; linked eval reports. |
| E3 | Alignment with recognised frameworks (NIST AI RMF, published safety policy) | Pass | Vendor's own mapping document references NIST AI RMF categories. |
| E4 | User-reported-harm channel and response posture | Pass | `safety@foo.ai`; public SLA; sample incident case study. |
| E5 | Content filters / classifiers documented, configurable per tenant where offered | Pass | Admin-console configurable; defaults documented. |
| E6 | Known-failure-mode disclosure (limitations, hallucination posture, refusal behaviours) | Pass | Model card published; linked to Ex-05. |
| E7 | Model deprecation policy and notice window | Pass | 90-day deprecation notice; past 24 months of deprecations listed. |

#### F. Operations & SLA

| # | Line | Status | Evidence |
|---|---|---|---|
| F1 | Written SLA with uptime target and credits | Pass | SLA dated 2026-11; 99.9% monthly target; service credits specified. |
| F2 | Public status page; 12 months of history available | Pass | `status.foo.ai`; reviewed; no undisclosed major outages. |
| F3 | Named 24×7 support channel for V1 tier | Pass | Zendesk + Slack Connect; SLA included. |
| F4 | Regional failover / multi-region documented | Pass | Multi-region active; failover procedure documented. |
| F5 | Scheduled maintenance windows published | Pass | Documented in trust center. |
| F6 | Rate limits documented; upgrade path clear | Pass | API docs; tier limits documented. |

#### G. Incident response

| # | Line | Status | Evidence |
|---|---|---|---|
| G1 | Named security contact reachable 24×7 | Pass | On-call rotation; test email answered within 2h. |
| G2 | Breach-notification SLA ≤ 72h | Pass | DPA §9.1. |
| G3 | Public history of past incidents (type, scope, resolution) | Pass | Trust center: 3 incidents in last 24 months with post-mortems. |
| G4 | Customer-facing post-mortem practice | Pass | Post-mortems linked from status page history. |
| G5 | Coordinated-disclosure posture documented | Pass | `security.txt`; PGP key. |
| G6 | Cyber insurance proof shared on request | Pass | See A6. |

#### H. Legal & contractual

| # | Line | Status | Evidence |
|---|---|---|---|
| H1 | Standard terms meet legal partner's redline list | Mitigated | 2 redlines pending (indemnification scope; audit right). Open with vendor counsel, target 2026-11-20. |
| H2 | IP indemnification for model output (scoped) | Pass | MSA §9; scope reviewed by legal partner. |
| H3 | Confidentiality and non-disclosure in force | Pass | MSA §12. |
| H4 | Export-control and sanctions clauses present | Pass | MSA §15. |
| H5 | Jurisdiction / dispute resolution acceptable | Pass | Reviewed. |
| H6 | Termination for convenience and for cause defined | Pass | MSA §10; 60-day notice for convenience. |
| H7 | Audit right (or equivalent trust-report cadence) | Mitigated | Pending H1; see above. |
| H8 | Price-change and auto-renewal notice windows | Pass | MSA §4; 60-day price-change notice. |

#### I. Exit / portability

| # | Line | Status | Evidence |
|---|---|---|---|
| I1 | Data export in a documented, non-proprietary format | Pass | JSON export; schema documented. |
| I2 | Deletion of tenant content on offboarding | Pass | DPA §8.2. |
| I3 | Custom prompts / configurations exportable | Pass | Admin console export. |
| I4 | Billing data exportable | Pass | CSV + API. |
| I5 | Tenancy can be split from a parent org without re-contract | Pass | Documented. |
| I6 | Vendor cooperation on migration-out documented | Mitigated | Written but no SLA on response time. Accepted risk, documented in H1 redline list. |

### 2.4 Findings (the artifact the packet produces)

The packet concludes with a short findings list. Only findings — not
every passed line — are actionable.

| # | Finding | Severity | Owner | Target |
|---|---|---|---|---|
| F-1 | Legal redlines H1 / H7 pending (indemnification scope; audit right). | High | legal partner | 2026-11-20 |
| F-2 | Image-signing root rotation policy not documented (C4). | Medium | security partner | 2026-12-15 |
| F-3 | Runway statement only; prefer audited financials on renewal (A2). | Low | team manager | renewal cycle |
| F-4 | Vendor migration-out SLA unquantified (I6). | Low | legal partner | renewal cycle |

Accepted-risk findings are recorded with the accepter's name. Blocking
findings must close before signature.

### 2.5 Sign-off block

Signed by:
- Team manager: <name>, 2026-11-11.
- Security partner: <name>, 2026-11-12.
- Legal partner: <name>, pending F-1 close.

The packet is not valid without all three signatures.

### 2.6 Review schedule

- **Standard review cadence:** annually (within 30 days before the
  renewal date).
- **Trigger-based review:** vendor announces an acquisition, a material
  change in sub-processors, an incident with customer impact; a new
  sub-processor added that is on the team's deny list; a change in the
  team's data handling obligations (Mod-110 Ex-04 update).
- **Interim health check:** quarterly skim of status-page history and
  security advisories; not a full packet re-run unless triggered.

## 3. Validation steps

Before sign-off:

1. **Is the tier assigned and justified in one sentence?** Running the
   full V1 checklist on a V3 vendor is theatre; running the V3 slim on
   a V1 vendor is negligence.
2. **Are reviewers named per section and have they actually reviewed?**
   A packet where the manager signs every section has not been
   reviewed; it has been self-attested.
3. **Is every Pass / Mitigated line backed by an evidence link, a doc
   path, or a dated email?** Lines without evidence are vibes.
4. **Did data handling (D-group) pass fully, or was something
   mitigated?** For V1 vendors a D-group Mitigated is a blocker unless
   the mitigation is contractually binding.
5. **Is the "no-fly" sub-processor list cross-checked against policy?**
   The vendor's sub-processors change; the team's policy exists.
6. **Did you actually try the vendor's security contact?** A `security@`
   mailbox that nobody reads is not an incident-response channel.
   Send a test message; wait for the response; record the latency.
7. **Did you read the DPA, or just pass it to legal?** Exercise-05 is
   where the DPA is read line by line; this packet records that it was,
   and names the Ex-05 output in evidence.
8. **Did you review public status-page history manually?** A "99.9%
   uptime" claim is not the same as a status page that shows the vendor
   recovered from three major outages in six months with credible
   post-mortems.
9. **Is the findings list separated from the checklist?** The packet's
   output is the findings; the checklist is the input. Mixing them hides
   signal.
10. **Is a review cadence and trigger list committed?** A packet that
    is valid "forever" is a packet that stops being valid tomorrow.

## 4. Rubric

Use this to grade a submitted packet (self-assessment or peer review).
Each criterion scores 0–3. Pass bar: **18 / 27**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Vendor tier assigned and scope-appropriate** | No tier | Tier stated, not justified | Tier stated + one-sentence justification | Tier stated + justification + explicit section inclusion/exclusion |
| R2 | **Reviewers named per section** | None | Some | All sections | All sections + evidence of each reviewer's actual engagement (email, note, Slack) |
| R3 | **Evidence anchored** | Few citations | Half | Most | Every Pass / Mitigated line has an evidence link, path, or dated contact |
| R4 | **Data-handling block depth** | Shallow | D1–D4 covered | D1–D9 covered | D1–D9 covered + explicit link to Ex-05 output + no-fly sub-processor check |
| R5 | **Corporate + financial + insurance covered** | Omitted | Mentioned | Covered | Covered with carrier, policy limits, and renewal date |
| R6 | **Incident response tested, not assumed** | Not tested | Email on file | Test email sent | Test email sent and response latency recorded |
| R7 | **Legal redlines tracked as findings** | Not tracked | Mentioned in prose | Tracked | Tracked with owner, target date, blocking vs. accepted status |
| R8 | **Exit / portability block completed** | Omitted | Mentioned | Covered | Covered with export format, deletion schedule, migration cooperation terms |
| R9 | **Review cadence + triggers committed** | None | Cadence only | Cadence + triggers | Cadence + triggers + where the packet lives + who is paged on triggers |

## 5. Common mistakes

- **Running the full V1 checklist on a V3 vendor.** Produces a 100-line
  packet nobody reads; the real risks on a V3 vendor are different and
  shorter.
- **Accepting sales-deck claims as evidence.** "We don't train on your
  data" on the home page is not a DPA clause; "ISO 27001 compliant" on
  the trust badge is not a certificate. Pull the artifact.
- **Letting the manager sign every section.** The packet becomes
  self-attestation. Section ownership matters.
- **Skipping the security-contact test.** A `security@` alias that
  bounces is a finding; discover it before the first incident, not
  after.
- **Not cross-checking sub-processors against policy.** The DPA
  discloses sub-processor X; the team's data-and-IP handling policy
  forbids X. If no-one cross-checks, the team signs itself out of its
  own policy.
- **Treating "SOC 2 Type II" as a binary pass.** Type II reports vary
  widely in scope, period, and exceptions. Pass is "we read the report
  and it covers the controls we care about for the period we care
  about" — not "they handed us a cover page." Exercise-05 does the
  depth read; this packet records that it was done.
- **Mixing findings and checklist.** The packet's purpose is the
  findings list. A packet that is one long form with no findings is a
  packet that has no output.
- **No review trigger for sub-processor changes.** Sub-processors
  change more often than the annual renewal. If a change does not
  trigger a mini-review, the no-fly list is purely nominal.
- **Not naming an acquirer or ownership change trigger.** AI vendors
  are in a consolidating market; a vendor's posture at acquisition can
  change overnight, and the packet must name the trigger that opens a
  re-review.
- **Omitting insurance.** A vendor that cannot show cyber or E&O
  insurance with named limits is a vendor whose indemnification clause
  has no backing when it actually matters.
- **Scoring every passed line and skipping the mitigations.** The
  mitigations are the interesting part. The packet should surface them,
  not hide them in the middle rows.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework)
  — the **Govern** function's **third-party risk** expectations
  (govern-6.1: *policies and procedures are in place to address AI risks
  and benefits arising from third-party entities*) are the direct
  structural anchor for this packet. The **Map** function's attention
  to *context and use* underwrites the vendor tiering in §2.1 — the
  checklist bar must be proportional to use-context. The **Measure**
  function's demand for *analysable, traceable* evidence is why every
  line requires a citation.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/)
  — the supply-chain-attack category is the direct motivation for the
  SBOM / signed-releases lines (C3, C4), the vendor-side CVE / pen-test
  history lines (B3, B5), and the sub-processor review (D5, D6). An AI
  vendor is a dependency; it deserves the same scrutiny.
- [MITRE ATLAS](https://atlas.mitre.org/) — the adversarial-threat
  taxonomy for ML systems. Used to sanity-check that product-security
  review (C6) addresses the right threats: prompt-injection, indirect
  data exfiltration, model-output tampering, tool-call abuse. The
  checklist uses ATLAS as the shared vocabulary rather than inventing
  threat names locally.

<!-- follow-up: once the completeness pipeline has shipped a second
cycle of module solutions, add a worked re-run of the packet one year
later, showing how the renewal-cycle review surfaces changes and feeds
back into exercise-05's DPA re-read. -->
