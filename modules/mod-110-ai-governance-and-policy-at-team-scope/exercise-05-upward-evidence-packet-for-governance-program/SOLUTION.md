# Exercise 05 — The Upward Evidence Packet for the Governance Program

> Module: `mod-110-ai-governance-and-policy-at-team-scope`
> Exercise type: **design** (worked packet + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is the **upward evidence packet** — the artifact the
team manager hands to their skip (or to a director, VP, legal, or
auditor) that answers, in one sitting: *does this team actually
have an AI governance programme, and what is the evidence?*

The reader of this packet is **not** a peer manager with the same
tooling. They do not have time to crawl the team's repo. They want
to know, for each of the team's governance commitments, two things:

1. **Does the control exist?** What is the artifact, who owns it,
   when was it last reviewed?
2. **Has the control fired?** When did it catch something? When did
   it fail? What happened?

The packet is **not** a pitch and **not** a self-review. It is a
curated, honest exhibit of the team's current governance state —
including the parts that are not done. A skip or an auditor who
reads a packet that claims completeness will go looking for gaps;
a skip who reads a packet that names its gaps with owners and
dates has a harder job finding something they did not already see.

Design decisions this solution encodes:

1. **One-page executive summary on top, exhibits below.** The skip
   reads the summary; the auditor reads the exhibits. Both readers
   are served without either being frustrated.
2. **Every claim links to the primary source.** No synthesised
   charts without the raw data link; no "the team has adopted…"
   without a path to the file that proves it. The packet is a
   viewing surface, not the source of truth.
3. **Gaps are declared, not hidden.** Governance maturity is not
   "everything is done"; governance maturity is *knowing what is
   not done, and when it will be*. Exhibits include a punch list.
4. **Metrics where they exist; absence stated explicitly where
   they do not.** Fabricated metrics — invented adoption rates,
   speculative savings, pseudo-statistics with no denominator — are
   the single fastest way to lose a skip's trust. The packet
   reports N, cadence, and limits.
5. **The packet is reproducible each cycle.** It is a template, not
   a one-off. A peer manager could regenerate the structure against
   their team's artifacts in the same amount of time.
6. **The packet names decisions the skip needs to make** — e.g.,
   whether to approve admission of a new high-risk AI tool, whether
   to adjust the risk-tolerance line, whether to allocate headcount
   to the backlog. Packets that do not ask for anything are
   information dumps; packets that ask for specific decisions
   invite action.
7. **The packet does not duplicate the policies.** It points to
   them. The team's `AI_POLICY.md`, `AI_DATA_POLICY.md`,
   `MCP_POLICY.md`, literacy programme, and incident runbook are
   already the sources; the packet is the exhibit.

## 2. Implementation — worked packet (`docs/governance-reviews/<YYYY-QN>-upward-packet.md`)

```markdown
# AI Governance Programme — Upward Evidence Packet — Q4 2026

**Team:** <team name>
**Prepared by:** <team manager name>
**Reviewed by:** <engineering director>, <legal liaison>,
<security liaison>
**For:** <skip name>, <skip's leadership chain>
**Period covered:** 2026-08-01 → 2026-10-31
**Companion artifacts in this packet:** linked inline; raw exports
and exhibit files are in `docs/governance-reviews/2026-Q4/`.

## 1. One-page executive summary

### 1.1 State of the programme

As of 2026-10-31, the team operates an AI governance programme
anchored in five policies, two rituals, one literacy programme,
and a tool inventory. Each is listed in §2 with a named artifact
path, a named owner, and the date of its most recent revision. The
programme is operational but incomplete; the explicit punch list
is in §6.

### 1.2 What happened in this period

- **One AI-adjacent incident** (SEV3) declared on 2026-09-14; full
  post-mortem at `docs/postmortems/2026-09-14-retrieval-leak.md`.
  Root cause: retrieval index included a path that was reclassified
  from D1 to D3 but the index config was not updated. Fixed; one
  regression fixture added; one eval row added; data policy
  revision log updated (see §3).
- **Two tool admissions** (new MCP server for issue tracking; new
  internal RAG endpoint). Admission reviews completed; artifacts
  in `docs/tool-admissions/`.
- **Four ADRs** on AI-adjacent decisions (`docs/adr/0041`–`0044`).
- **100% of team members** have completed the current literacy
  programme (N = 7 ICs + 1 manager; see §5). Of these, two were
  tier-upgraded User → Builder in the period and completed the
  additional modules before access was granted.
- **Three evaluation suites** gated releases (fairness,
  reliability, retrieval-grounding). All passing on the current
  main branch as of 2026-10-31; one failure in the period on
  retrieval-grounding, blocked the release and was resolved before
  merge.
- **One policy loosen** was declined (proposed exception to the
  D4 × T-Business cell in `AI_DATA_POLICY.md`). Decision recorded
  at `docs/adr/0043`.

### 1.3 Asks of the reader

1. **Approve admission** of <vendor X's code-review agent> as a
   T-Enterprise tool for D1/D2 content; the admission review
   (§7.1) is complete, and the escalation table requires skip
   sign-off at this tier.
2. **Adjust risk-tolerance line** for the retrieval index
   covering D3/D4 content: the current line says "manual review
   on every reclassification"; the Q4 incident suggests this is
   not scaling. Proposal at §7.2.
3. **Confirm quarterly cadence** is right. The team has added two
   off-cycle refreshes this quarter and the ritual is holding;
   no change requested but flagged for skip awareness.
4. **Headcount check-in** on the backlog in §6; two rows are
   >90 days without progress.

## 2. Programme artifacts (controls inventory)

| # | Artifact | Scope | Owner | Last revised | Status |
|---|---|---|---|---|---|
| A1 | `docs/AI_POLICY.md` | Top-level team AI policy | <manager> | 2026-10-04 | Live |
| A2 | `docs/AI_DATA_POLICY.md` | Data × tool matrix, IP, confidentiality (mod-110 ex-04) | <manager> + legal | 2026-09-22 | Live |
| A3 | `docs/MCP_POLICY.md` | Tool authorisation (mod-106 ex-03) | <manager> | 2026-09-10 | Live |
| A4 | `docs/AGENTS.md` | Delegation contract (mod-106 ex-02) | <manager> | 2026-08-28 | Live |
| A5 | `docs/INCIDENT_RUNBOOK.md` | Incident response, AI-specific signals (mod-109 ex-01) | <on-call lead> | 2026-10-18 (post-incident revision) | Live |
| A6 | `docs/AI_LITERACY.md` + `docs/onboarding/completed/` | Literacy programme (mod-110 ex-02) | <manager> | 2026-07-12 | Live; renewal in Q1 2027 |
| A7 | `docs/AI_INVENTORY.md` | Tools and models in use | <manager> | 2026-10-26 | Live |
| A8 | `docs/AI_RACI.md` | Accountability per class of change | <manager> | 2026-07-05 | Live |
| A9 | `docs/AI_ESCALATION.md` | Escalation targets | <manager> | 2026-07-05 | Live |
| A10 | `docs/AI_REGS_REGISTER.md` | Regulations applicable | <manager> + legal | 2026-09-30 | Live |
| A11 | `docs/AI_CONTINGENCY.md` | Third-party fallback plans | <manager> | 2026-06-15 | Live; drill scheduled Q1 2027 |
| A12 | `docs/AI_FEEDBACK_INTAKE.md` | Feedback inbox (mod-110 ex-01 GOVERN 5.1) | <IC name> | 2026-08-14 | Live |
| A13 | `docs/AI_PRINCIPLES_MAP.md` | Mapping to Microsoft RAI and Google AI Principles (mod-110 ex-03) | <manager> | 2026-10-11 | Live |
| A14 | `.github/pull_request_template.md` + `AGENTS.md` review items | Enforcement in review (mod-103 ex-01) | <manager> | 2026-09-07 | Live |
| A15 | `docs/evals/` | Eval suites (mod-103 ex-02) | <IC name> | continuous | Live; 3 suites current |
| A16 | `docs/governance-reviews/<YYYY-QN>.md` | Quarterly governance review ritual | <manager> | 2026-10-26 | Live |

For each row above, the linked artifact is the primary source. The
reviewer should click through for anything that warrants more
scrutiny; the packet does not duplicate contents.

## 3. Evidence of firing

Governance that exists only in documents and never catches anything
is not a programme. Below is what the programme caught in the
period, with primary sources.

### 3.1 Incident (one, SEV3)

- **2026-09-14 — Retrieval index exposed reclassified D3 content.**
  Post-mortem: `docs/postmortems/2026-09-14-retrieval-leak.md`.
  - What prevented a worse incident: the team's MCP policy had
    the retrieval tool in T1 with untrusted-input handling, so
    the content did not auto-flow to downstream tools. See MCP
    policy §3.2 and the post-mortem §Mitigations-that-held.
  - What failed: the D3 reclassification (a specific telemetry
    table was moved from D1 to D3 in the data policy on
    2026-09-04) did not trigger a retrieval-index audit. Root
    cause in the post-mortem.
  - What changed: regression fixture
    `evals/retrieval/2026-09-fixture.jsonl` added; the data
    policy revision process now includes a "touch the retrieval
    index" step; ADR `docs/adr/0042` records the change.
  - Open followups: zero as of 2026-10-31.

### 3.2 Policy fires (review and automation)

- **Pre-commit secret scanner** blocked 3 attempted commits of
  content matching D5 patterns (detail counts, not content).
  Source: `docs/audit/paste-blocks.ndjson` filtered to the period.
- **PR review items** on AI-assisted PRs:
  - 18 PRs in the period flagged "author must explain diff"; 2
    were revised after the IC could not explain the generated
    code. See PR #3421 and PR #3488.
  - 1 PR blocked at review for a hallucinated identifier; see PR
    #3501 and the subsequent regression fixture
    `evals/code-gen/2026-10-fixture.jsonl`.
- **Tool admission reviews** gated 2 new tools; 1 additional
  proposal was declined (`docs/tool-admissions/<tool>-declined.md`).

### 3.3 Eval gates

Eval suites and their firings:

| Suite | Fixtures | Runs this period | Failures | Resolution |
|---|---|---|---|---|
| Fairness (`evals/fairness/`) | 12 | 9 (per release gate) | 0 | n/a |
| Reliability regression (`evals/regression/`) | 47 (3 added this period) | 51 (every main-blocking change) | 1 (retrieval-grounding on 2026-10-04) | Fix in PR #3488 before merge |
| Retrieval grounding (`evals/retrieval/`) | 18 (1 added this period) | 12 | 0 after fixture was added on 2026-09-15 | n/a |

### 3.4 Feedback intake triage

- 11 items received (breakdown by source and class in
  `docs/governance-reviews/2026-Q4/feedback-breakdown.md`).
- Dispositions: 4 → eval fixture; 2 → policy ADR; 1 → onboarding
  update; 3 → wontfix with recorded reason; 1 → open.
- Longest open item: 21 days (user-visible retrieval answer
  grounding complaint; owner <IC>; target 2026-11-20).

## 4. People

- **Headcount on the team:** 7 ICs + 1 manager.
- **Role tiers (mod-110 ex-02):** 2 User-tier, 4 Builder-tier,
  2 Operator-tier (manager + one IC).
- **Literacy programme completion:** 100% for current tier.
- **Tier upgrades in period:** 2 User → Builder.
- **New hires in period:** 1 (completed M1–M5 before AI tool
  access was enabled; completed M6–M7 within 30 days; tier =
  Builder).
- **Named roles:** <IC> owns feedback intake triage; <IC> owns
  MCP admission reviews; <IC> owns eval suite maintenance;
  <on-call lead> owns incident runbook.

## 5. What the programme costs

- **Manager time:** ~2 hours/week on governance review, 1 full
  day per quarter for the ritual, ~30 minutes per admission
  review, ~2 hours per incident post-mortem. ~4% of calendar.
- **IC time:** literacy modules (~3–6 hours per IC per year
  depending on tier); eval suite maintenance embedded in
  engineering work; feedback triage ~1 hour/week for the named
  triager; incident response as needed.
- **Tooling:** no new spend in the period. Existing pre-commit
  scanner is shared with the org-level security team; MCP
  config/lint is in-tree.
- **Cost of **not** having the programme:** unquantified. The
  Q4 incident's mitigations held partly because the MCP policy
  existed; attempting to monetise that counterfactual is not
  credible. The honest statement is: "the programme did not
  prevent the incident; it bounded the blast radius, and the
  post-mortem shows how."

## 6. Punch list (what is not done)

Visible gaps with owner and target. Rows crossed off between
cycles are retained with a strike-through and the completion
commit, so a skip reading two cycles side-by-side can see
movement.

| # | Gap | Owner | Target | Status |
|---|---|---|---|---|
| G1 | Accessibility eval fixtures (`evals/accessibility/`) placeholders only | <IC> | On first user-facing generative feature | Pending |
| G2 | DLP / pre-paste hook extension to <IDE X> | Security liaison | 2026-12-15 | 94 days open; in security team's backlog |
| G3 | Contingency drill for third-party model API vendor | <manager> | 2027-01-31 | Scheduled |
| G4 | Hiring AI policy (`docs/HIRING_AI_POLICY.md`) drafted but not yet exercised (no AI-assisted hiring loop has run) | <manager> + recruiter | On first AI-assisted hiring loop | Pending |
| G5 | Legal review of `docs/AI_PRINCIPLES_MAP.md` §4 (Google AI Principles section) after Google's early-2025 revision | Legal liaison | 2027-02-01 | Pending; off-cycle trigger logged |
| G6 | Customer-facing labelling policy for generative output | PM + manager | On first customer-facing generative feature | Pending |

Rows G2 and G5 are >90 days open; §1.3 asks the reader for a
headcount check-in on G2 and surfaces G5 as a hard dependency on
legal capacity.

## 7. Decisions requested

### 7.1 Admit <vendor X's code-review agent> as T-Enterprise for D1/D2

- Admission review: `docs/tool-admissions/vendor-x-code-review.md`.
- Vendor trust surface reviewed: DPA signed, non-training
  commitment present, residency commitments reviewed, breach
  history reviewed by security.
- Proposed tiering: T-Enterprise; allowed cells D1 and D2 only
  (not D3/D4/D5/D6). Rationale in admission review.
- Required owner: <IC>.
- Required integration gate: admission to `AI_INVENTORY.md`,
  MCP policy row, and literacy M1/M2 refresh reference.

Decision requested: **approve** or **approve with modification**
(e.g., D1 only; shorter review cadence; additional pilot gate).

### 7.2 Adjust risk-tolerance line on retrieval reclassification

- Current line (`AI_DATA_POLICY.md` §4.2): "manual review on every
  reclassification of a source path that feeds a retrieval index."
- Problem revealed by the Q4 incident: reclassifications happen
  more often than manual review scales to.
- Proposal: add an automated audit that flags any retrieval
  index whose source paths include a row whose class was changed
  in the last 7 days; manual review only on flags.
- Rationale: changes the cadence from "every reclassification" to
  "every reclassification that could affect a live retrieval
  config," which is strictly tighter (because the automated audit
  will catch cases a human would miss) and more sustainable.
- Owner: <IC>, with security liaison review.

Decision requested: **approve** the proposal or **decline, retain
manual-only review**.

## 8. How this packet was produced

- The packet is regenerated each quarter during the quarterly
  governance review ritual (mod-110 ex-01 GOVERN 1.5). It is not
  a bespoke document.
- The §2 controls inventory is a `git ls-files docs/` filter plus
  the quarterly review's revision-date query, not hand-maintained.
- The §3 evidence sections are produced by:
  - `grep` over the post-mortem folder for the period;
  - `cat` of `docs/audit/paste-blocks.ndjson` filtered by date;
  - PR API query for review-item fires;
  - the eval suite's per-run log in `evals/results/`.
- The §4 people section is a tier-roll of
  `docs/onboarding/completed/`.
- The §5 cost section is manager estimate (time) + accounting
  (spend); the time estimate has been stable across three cycles.

## 9. What this packet is not

- Not a pitch. There are gaps; they are declared.
- Not a legal attestation. Legal has reviewed the controls
  inventory structure; interpretation lives with counsel.
- Not a comparison to industry benchmarks. The team does not
  have access to comparable benchmarks for team-scope AI
  governance and does not invent them.
- Not a request for additional governance scope beyond the
  team's responsibility (board oversight, enterprise-wide risk
  tolerance belong elsewhere; see `docs/AI_ESCALATION.md`).
- Not a dashboard. Dashboards update continuously; this packet
  is a quarterly exhibit. The dashboard equivalents (eval pass
  rate, pre-commit fire rate, open feedback items) are linked
  from §3.

## 10. Prior packets

- 2026-Q3: `docs/governance-reviews/2026-Q3-upward-packet.md`.
- 2026-Q2: `docs/governance-reviews/2026-Q2-upward-packet.md`.

The skip is invited to compare across cycles. Movement on the
punch list is intentionally legible by diff.

## 11. References

- NIST AI Risk Management Framework — GOVERN 1.5 (periodic
  review); GOVERN 4.2 (teams document and communicate risks and
  impacts). This packet is the team's fulfilment of GOVERN 4.2
  upward.
- NIST AI 600-1 Generative AI Profile — where relevant to §3
  incident and §7 decisions.
- EU AI Act, Regulation (EU) 2024/1689 — literacy evidence in
  §4 ties to Article 4.
- Microsoft Responsible AI; Google AI Principles — the team's
  mapping is A13 in §2; this packet is the upward exhibit of
  that mapping's operation.
- OWASP Machine Learning Security Top 10; MITRE ATLAS —
  referenced via the incident framing in §3.1.
```

## 3. Validation steps

To validate an upward packet before sending it:

1. **Does every claim in §1 (executive summary) link to the
   specific exhibit section that backs it?** A one-page summary
   with no clickable path to the raw underlying artifact is a
   summary the reader cannot verify.
2. **Does every control artifact have a path that exists in the
   repo and a last-revised date?** If a row names an artifact that
   does not exist, the packet is false by omission. Fix the row
   or fix the artifact before publishing.
3. **Does the packet declare at least one gap?** A packet with no
   gaps is not credible — every real governance programme has
   gaps. A packet with no gaps should be re-read with that in
   mind.
4. **Does every ask (§1.3, §7) name a specific decision?** "The
   skip should consider our progress" is not a decision. "Approve
   admission of X as T-Enterprise for D1/D2 only" is.
5. **Do the incident and policy-fires sections use real numbers,
   with denominators?** "We blocked many attempts" is not a
   number. "Three attempts in the period; see log" is.
6. **Are the gaps named by owner and target date?** A gap list
   without accountability is a list of problems; with owner and
   date it is a plan.
7. **Does the packet point at prior cycles so diffs are
   reviewable?** Missing prior-cycle references means the reader
   cannot see motion; motion is what a skip actually cares about.
8. **Does §9 ("What this packet is not") explicitly disclaim legal
   interpretation and comparison to invented benchmarks?** Packets
   without this section tend to over-claim; packets with it tend
   to be taken seriously.
9. **Could a peer manager regenerate the §2–§4 sections against
   their team's artifacts using the same queries?** If yes, the
   packet is reproducible; if no, it is a bespoke effort that will
   not survive the first quarter where the manager is on leave.

## 4. Rubric

Grade an upward packet. 0–3 each. Pass bar: **20 / 30**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| U1 | **One-page summary quality** | Multi-page summary or no summary | Summary present | Summary + linked claims | Summary + linked claims + specific asks |
| U2 | **Controls inventory specificity** | Narrative only | Some artifacts named | All artifacts named + owners | All named + owners + paths + last-revised dates |
| U3 | **Firing evidence** | None | Incidents only | Incidents + policy fires + eval gates | All with numbers + denominators + primary-source links |
| U4 | **Honest gap declaration** | No gaps declared | Gaps named in prose | Gap table | Gap table + owner + target + aging |
| U5 | **Decisions requested** | None | One vague ask | Specific asks | Specific asks + required artifacts attached + acceptable modifications pre-named |
| U6 | **Reproducibility** | Bespoke document | Partially templated | Clearly templated + queries named | Templated + a peer could regenerate for their team in the same effort |
| U7 | **Cost transparency** | Cost not discussed | Dollar cost only | Time + dollar cost | Time + dollar + "what the programme does not cost" (unquantified counterfactuals named honestly) |
| U8 | **Prior-cycle diff legibility** | No reference to prior | Prior listed | Prior listed + strike-through on closed gaps | Prior linked + diff explicit + motion is legible without reading two documents |
| U9 | **Not-a-pitch tone** | Reads as marketing | Mixed | Factual | Factual + explicit "what this packet is not" section |
| U10 | **Scope discipline** | Claims enterprise-wide governance | Boundary implied | Named as team-scope | Team-scope + escalation table referenced + does not duplicate enterprise policy |

## 5. Common mistakes

- **Writing the packet like a brag reel.** Any sentence that would
  feel at home on LinkedIn is a sentence that will hurt the
  packet's credibility. Delete it and replace with a specific
  artifact reference.
- **Fabricating metrics.** "Governance programme has saved X
  hours," "adoption is at Y%," "satisfaction is Z%" — all of these
  without instrumentation are fabrications. Report what the team
  actually measures; say explicitly what it does not measure.
- **Omitting the gap list.** The gap list is the most important
  part of the packet. Skips who have read many of these learn to
  look for it first; a packet without one reads as either naive or
  dishonest.
- **Asking for nothing.** A packet that does not request any
  decision is an information dump. The skip's time is more
  valuable than that; name the decisions.
- **Duplicating the policies inline.** The packet is an exhibit,
  not a reprint. Link; do not duplicate. A 30-page packet is a
  packet the skip will scan, not read.
- **One-off formatting.** A hand-crafted packet that cannot be
  regenerated next quarter is a packet that will miss the next
  quarter. The §2–§4 sections should be produced by named queries
  and the queries should be documented in §8.
- **Over-claiming governance scope.** Team-scope packets that
  describe board oversight, enterprise-wide risk tolerance, or
  corporate legal commitments are confused about scope. Those are
  the skip's job, not the team manager's; the packet names the
  escalation path and stops there.
- **No "What this packet is not" section.** Without it, the packet
  is read as a legal attestation. With it, the packet is read as
  what it is — an engineering manager's exhibit of a team's
  governance programme.
- **Metrics with no denominators.** "We blocked three attempts" is
  evidence; "we blocked most attempts" is a story. Report N; if
  you do not have N, say so.
- **Cost section that only lists spend.** Governance programmes
  cost calendar more than money for teams of this size. A cost
  section that omits manager and IC time is incomplete.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) —
  GOVERN 1.5 (ongoing monitoring and periodic review) and GOVERN
  4.2 (teams document and communicate AI risks and impacts
  broadly) are the structural anchors; this packet is the team's
  fulfilment of GOVERN 4.2 in the upward direction.
- [NIST AI 600-1: Generative AI Profile](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf) —
  the vocabulary the packet uses when naming incident classes
  (confabulation, information integrity, data privacy,
  information security, IP) in §3.1 and §7.
- **EU AI Act, Regulation (EU) 2024/1689** — Article 4 literacy
  evidence in §4 of the packet.
- [Microsoft Responsible AI](https://www.microsoft.com/en-us/ai/responsible-ai)
  and [Google's AI Principles](https://ai.google/responsibility/principles/) —
  the mapping artifact (A13) summarises the team's operational
  decomposition; this packet is the upward exhibit of that
  mapping's operation.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/)
  and [MITRE ATLAS](https://atlas.mitre.org/) — referenced via
  incident framing and retrieval-security evidence in §3.
- [VeriSwarm Trust Center](https://veriswarm.ai/trust) —
  practitioner reference for how a vendor publishes trust-posture
  evidence externally. The team's upward packet is the internal
  analogue — content and format differ because the audience
  differs (internal skip vs. external customer), but the
  evidence-first discipline is the same. Cited as practitioner
  example only.
