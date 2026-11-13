# Exercise 04 — Data, IP, and Confidentiality: Author the Team Policy

> Module: `mod-110-ai-governance-and-policy-at-team-scope`
> Exercise type: **design** (worked policy + decision rationale + grading rubric)

## 1. Solution overview

The deliverable is `docs/AI_DATA_POLICY.md`, the team's authored
answer to four questions that the organisation-level AI policy
cannot answer for them:

1. **What data classes exist in this team's work, and which may be
   sent to which AI tools?** The org policy says "no PII in
   consumer-tier ChatGPT"; the team policy says *which exact system
   or repo path contains PII for this team, and which approved tool
   is the correct destination for the specific workflow that
   touches it.*
2. **Who owns the IP in AI-assisted output, and how is that
   recorded?** Commit-trailer convention, attribution rule, licence
   compatibility for any third-party content that enters a prompt.
3. **What confidentiality obligations constrain the team's AI use?**
   Customer contracts, NDAs with partners, non-public
   roadmap/financials — which of these create specific prohibitions
   on specific tools and workflows.
4. **How is the policy enforced and evidenced?** Pre-commit hooks,
   agent configuration, review items, incident runbook, audit log.

The policy sits between the general corporate AI policy and the
daily workflow — it is the thing the IC reads before pasting.

Design decisions this solution encodes:

1. **Data classes come from the team's own systems, not from an
   abstract schema.** Each row names the system, the path, and the
   owner. "PII" as a label is not enough; the policy names *where
   PII lives in this team's world* so the IC can tell at a glance.
2. **Policy is tool-specific.** Teams use many AI tools (code
   assistant, consumer chat, an internal RAG, a vendor model API,
   an MCP-enabled agent). Each has different data boundaries. One
   matrix cell per (data class × tool) is explicit.
3. **IP policy separates three things that often get conflated:**
   (a) who owns AI-assisted output the team produces; (b) what
   content from third parties is allowed into a prompt; (c) what
   attribution is required on the team's work.
4. **Confidentiality covers obligations that outlive employment.**
   Customer NDAs, partner NDAs, embargoed roadmap content —
   enforcement is independent of whether the person is "trusted"
   with the data for internal use.
5. **Enforcement names the specific mechanism, not an exhortation.**
   "The team is careful" is not a mechanism. A pre-commit hook that
   rejects a known PII pattern is. The policy names what each row
   has today and what is still "pending".
6. **Policy is scoped to the team's current reality and names its
   own evolution path.** When the team admits a new tool, the matrix
   grows; when a new data class appears (SOC 2 audit, a new
   customer contract with stricter terms), the row is added. The
   evolution trigger list is explicit.
7. **Nothing in the policy conflicts with higher-scope policy.**
   Where the org policy is stricter, the team policy says so; the
   team policy never loosens what the org forbids.

## 2. Implementation — worked policy (`docs/AI_DATA_POLICY.md`)

```markdown
# Team AI Data, IP, and Confidentiality Policy — v1.0

**Team:** <team name>
**Owner:** <team manager name>
**Legal liaison:** <name>
**Last revised:** 2026-11-12
**Next scheduled revision:** 2027-02-12 (quarterly)
**Off-cycle revision triggered when:**
- A new AI tool is admitted (`docs/AI_INVENTORY.md`).
- A customer contract with new data-handling terms is signed.
- The organisation-level AI policy is revised.
- A new data class appears in the team's systems (new service,
  new table, new vendor integration).
- An AI-adjacent data incident is declared.

## 1. Purpose and relation to higher-scope policy

This document is the team's implementation of the organisation's
AI policy for the specific systems, data, and tools this team
uses. **It never loosens the organisation policy.** Where it
conflicts, the organisation policy wins. Where the organisation
policy is silent, this document decides.

The organisation policy is at `<link to corporate policy>`. This
document references it in rows below; this document is the
*operational* half.

## 2. Data classes in this team's work

Each row is a data class **as it exists in this team's systems**,
not an abstract definition. If an IC asks "is X PII?", the answer
is in the "Where it lives" column.

| ID | Class | Definition for this team | Where it lives (named systems/paths) | Owner |
|---|---|---|---|---|
| D0 | **Public** | Content the team has published or that is otherwise public domain or permissively licensed. | `README.md`, release notes, public blog drafts after legal clear. | Team manager |
| D1 | **Internal** | Non-sensitive internal content; disclosure would be awkward but not harmful. | Day-to-day Slack in team channels, internal design docs under `docs/` excluding §D2–§D4 subpaths, Jira tickets without customer names. | Team manager |
| D2 | **Confidential** | Internal content whose disclosure would damage the business: roadmaps, financials, pre-announcement product work, team composition, vendor pricing, draft contracts. | `docs/roadmap/`, `docs/finance/`, `docs/vendor-pricing/`, pinned Slack in `#team-admin`. | Team manager + finance liaison |
| D3 | **Customer data** | Data from or about a specific customer: tenant IDs, usage logs, support tickets, support attachments, meeting notes with customer names. | `telemetry.<tenant>.*` tables, `#support-*` Slack channels, `support-attachments/` S3, Zendesk. | Customer support lead + security |
| D4 | **Regulated or PII** | Personal data, special-category data, payment data, health data, data under sectoral rules the team touches. | `users` and `profiles` tables; any CSV exported from those; GDPR data-subject-request work under `docs/dsr/`. | Security + legal |
| D5 | **Secrets** | Credentials, tokens, API keys, private keys, prod connection strings. | Vault, `.env`, deploy manifests, CI secrets. | Security |
| D6 | **Third-party restricted** | Content covered by a specific NDA or licence (partner spec drafts, pre-release vendor docs, customer-supplied IP). | `docs/partners/<partner>/` with per-folder README naming the agreement. | Team manager + legal |

### 2.1 How to classify when unsure

- Pattern match: ID → table name / path. If the content came from
  a D3 or D4 source, it is D3/D4 regardless of how summarised it
  is downstream.
- When ambiguous: default to the stricter class. Escalate to the
  owner listed above; the owner's call is binding and recorded as
  a one-line note in `docs/AI_DATA_POLICY.md` ⟶ revision log.
- "This looks like it would be fine" is not a classification. Use
  the matrix.

## 3. AI tools in use

The team's AI inventory (`docs/AI_INVENTORY.md`) is the source of
truth. For this policy, each tool is placed in a tier describing
its data boundary:

| Tier | Boundary definition | Examples in this team |
|---|---|---|
| **T-Enterprise** | Enterprise agreement with the vendor: zero training on inputs; data-residency commitments; audit controls; signed DPA. | Enterprise Claude via <approved enterprise gateway>; internal RAG backed by Bedrock with account-local models. |
| **T-Business** | Business-tier SaaS with contractual non-training on inputs, but without full enterprise controls. | Code assistant business plan with on-by-default non-training; GitHub Copilot Business. |
| **T-Consumer** | Consumer/free-tier SaaS without non-training guarantees. | Public chat.openai.com, consumer Gemini, consumer Claude. |
| **T-Local** | Model running on the team's infrastructure or on an IC's device, with no network egress for inference. | Locally-run inference for small tasks; CI-only models. |
| **T-Vendor-restricted** | Vendor-specific restrictions that make the tool unsuitable for a specific data class. | <any tool the team has marked explicitly restricted>. |

Tool admission (adding a new row to the inventory) requires the
admission review process in `docs/tool-admissions/` and a row added
to §4 below.

## 4. Data × tool matrix

The cell value is the policy. **"Prohibited"** means the IC must
not place this data into this tool; **"Allowed, with named
condition"** means the IC may do so if the condition is met;
**"Allowed"** means no additional condition.

| Data class ↓ / Tool tier → | **T-Enterprise** | **T-Business** | **T-Consumer** | **T-Local** |
|---|---|---|---|---|
| **D0 Public** | Allowed | Allowed | Allowed | Allowed |
| **D1 Internal** | Allowed | Allowed | Prohibited | Allowed |
| **D2 Confidential** | Allowed | Allowed, if the specific tool's DPA covers the content | Prohibited | Allowed |
| **D3 Customer data** | Allowed, if the specific tool's DPA covers the content *and* the content is scoped to work for that customer (no cross-customer prompting) | Prohibited | Prohibited | Allowed, if the content stays on device and is not exported |
| **D4 Regulated / PII** | Allowed only in the team's internal RAG with explicit per-request legal flag; otherwise prohibited | Prohibited | Prohibited | Prohibited unless fully anonymised; anonymisation technique documented in `docs/anonymisation.md` |
| **D5 Secrets** | **Prohibited** | **Prohibited** | **Prohibited** | **Prohibited** |
| **D6 Third-party restricted** | Allowed, if the specific agreement permits AI-tool use; otherwise prohibited | Prohibited unless the agreement explicitly permits | Prohibited | Prohibited unless the agreement explicitly permits |

### 4.1 Reading the matrix

- An IC pasting into a code assistant: match the tool to the
  column, match the content to the row, read the cell. If the cell
  says *Prohibited*, do not paste. If it says *Allowed, with named
  condition*, the condition is a non-optional checklist item, not
  a vibe.
- Ambiguity in the row → use §2.1. Ambiguity in the column → the
  tool is probably not yet in `AI_INVENTORY.md`; do not use it
  until it is admitted.
- The matrix is intentionally conservative. A looser matrix that
  "trusts the IC" fails in review and fails in audit.

### 4.2 Retrieval and agent memory

A tool with retrieval over a corpus, or with long-term memory, is
treated as "receiving" every data class present in that corpus or
memory. Cells are applied at retrieval, not just at paste. The
retrieval index for each team agent (see `AGENTS.md`) is named in
`docs/AI_INVENTORY.md` with its source paths; those paths determine
the agent's maximum data class.

## 5. Intellectual property

### 5.1 Ownership of AI-assisted output

Team-authored output produced with AI assistance is owned by the
same party that would own a non-AI-assisted equivalent under the
team's existing IP agreements (employment agreement; contractor
IP assignment). AI assistance does not change ownership.

The team's commit convention:
- Changes produced with substantial AI assistance include a
  trailer in the commit message, e.g.
  `AI-Assisted: yes (<tool>, <approximate scope>)`.
- Changes produced by an agent on behalf of a human include a
  co-author trailer, e.g.
  `Co-Authored-By: <assistant> <noreply@…>`, with the human as
  the primary author.
- The human author is accountable for every line, per the team's
  review discipline (mod-103 ex-01 and mod-110 ex-01 GOVERN 2.1).

### 5.2 What third-party content may enter a prompt

- **Permissively-licensed content** (MIT, Apache 2.0, BSD,
  CC-BY/CC-BY-SA with attribution preserved) may enter a prompt.
- **Copyleft content** (GPL, AGPL) may enter a prompt only when
  the resulting output is intended to be released under compatible
  terms; otherwise it must not enter a prompt.
- **Customer-supplied content** is governed by the customer
  contract (see §6).
- **Partner / third-party restricted content** (D6) is governed
  by the specific agreement; the agreement wins.
- **Content whose licence is unknown** must not enter a prompt.

### 5.3 Attribution on team output

- User-facing AI-generated content is labelled per the team's
  policy (`docs/AI_POLICY.md` §Labelling; see also mod-110 ex-03
  §3.5 Transparency row).
- Internal code with AI assistance uses the commit trailers in §5.1.
- Documentation drafted with AI assistance notes it in the doc
  frontmatter if the document is published externally.

### 5.4 Model-output IP posture

- The team does not treat AI tool outputs as copyrightable in and
  of themselves; outputs become part of the team's work when a
  human reviews, edits, and commits.
- The team's product does not re-sell raw model output as a
  product deliverable without explicit review.
- Where a vendor contract addresses IP on outputs, the contract
  governs; the team's policy does not override it.

*Editorial note:* the state of copyright over AI-generated output
varies by jurisdiction and remains in active legal development.
Legal counsel is the canonical source; this section reflects the
team's operational posture, not a legal interpretation. The
off-cycle trigger list includes "legal publishes revised IP
guidance for AI-generated output."

## 6. Confidentiality

### 6.1 Contractual constraints

- **Customer contracts** — some customer MSAs restrict how their
  data may be processed (region, subprocessors, non-training
  commitments). Those restrictions override the §4 matrix downward.
  The customer-support lead maintains
  `docs/customer-data-constraints.md` with one row per customer
  whose contract adds a constraint.
- **Partner / vendor NDAs** — the D6 data class above; each folder
  in `docs/partners/<partner>/` has a README naming the agreement
  and its AI-use terms.
- **Employment agreements** — ICs' assignment and
  confidentiality obligations continue to apply; this policy does
  not relax them.

### 6.2 Embargoed internal content

- Pre-announcement product work is D2 Confidential and governed by
  the §4 matrix.
- Pre-public financial content is D2 and additionally may require
  finance sign-off for any AI use; see
  `docs/finance-ai-constraints.md`.
- Hiring and performance content (mod-108) has its own policy
  constraints documented in `docs/HIRING_AI_POLICY.md` and
  `docs/PERF_REVIEW_AI_POLICY.md`.

### 6.3 Sharing of AI outputs

- The team's AI outputs (summaries, transcripts, generated text
  or code) inherit the classification of their inputs.
- A summary of D4 data is D4 data. Downstreaming "it's just a
  summary" does not reclassify.

## 7. Enforcement

| Row | Mechanism | Status |
|---|---|---|
| D5 Secrets in any tier | Pre-commit hook + pre-paste scanner at the IDE level where available. | **Live.** |
| D4 Regulated / PII in consumer-tier | DLP / pre-paste hook; where the hook is not installed, the IC is personally responsible; onboarding (mod-110 ex-02 M2) is the training. | **Partially live** — pending extension to <IDE X>. |
| D3 Customer data cross-customer prompting | Review item on retrieval-index changes; logged. | **Live** on retrieval; manual elsewhere. |
| IP commit trailer convention | Pre-commit hook or commit template; PR template reminder. | **Live** (commit template). |
| Tool admission review | Admission folder gating config change. | **Live.** |
| Quarterly matrix review | Calendar item + §8 revision log. | **Live.** |

Rows marked "partially live" or "manual" are the backlog;
`docs/governance-reviews/<YYYY-QN>.md` is where their progress is
tracked.

## 8. Evidence and audit

- **Revision log** — every revision to this file is a commit in
  git with the author, date, and summary. `git log docs/AI_DATA_POLICY.md`
  is the audit trail.
- **Policy-fired log** — the pre-commit hook logs blocked paste
  patterns (counts, not content) to `docs/audit/paste-blocks.ndjson`.
  Content is not logged; only the pattern class and file path.
- **Incident cross-reference** — any AI-adjacent data incident
  post-mortem (`docs/postmortems/`) cross-references which row of
  this policy should have prevented it and whether the row did.

## 9. How to change this policy

1. Open a PR editing this file; include a one-line revision-log
   entry.
2. Required reviewers: team manager + legal liaison + security
   liaison (where the change touches D3/D4/D5/D6).
3. Changes that *loosen* a cell require an ADR under `docs/adr/`;
   changes that *tighten* require only the revision-log entry.
4. Off-cycle triggers (frontmatter) are *mandatory* reviews, not
   optional. A trigger that is not responded to is a flag on the
   quarterly governance review.

## 10. What this policy is not

- It is **not** a legal interpretation. Legal counsel is the
  canonical source for interpretation.
- It is **not** a complete enumeration of every data boundary in
  the organisation; it is the team-scope operationalisation of
  the organisation-scope policy.
- It is **not** a static artifact; the whole point is the quarterly
  review and the off-cycle refresh.

## 11. References

- Organisation AI policy — `<link>`. This document is a team-scope
  extension; the org policy is authoritative where they conflict.
- EU AI Act, Regulation (EU) 2024/1689 — Article 4 (literacy)
  cross-referenced for the training half of the enforcement gate
  (see mod-110 ex-02); Articles on data governance referenced by
  the D4 row where applicable.
- NIST AI Risk Management Framework — MAP 4.1 (characterise data
  origins); MEASURE 2 (ongoing measurement of data risks); MANAGE
  4 (treatment of residual risk); GOVERN 1.6 (inventory) — this
  policy is the per-tool-per-class decomposition that those
  functions require at team scope.
- NIST AI 600-1: Generative AI Profile — data privacy, information
  integrity, and intellectual property categories shape §4 and §5.
- OWASP Machine Learning Security Top 10 — relevant to D3/D4
  retrieval review items (data poisoning, membership inference,
  model output as exfiltration path).
- MITRE ATLAS — adversarial-threat taxonomy for incident framing.
- VeriSwarm Trust Center — practitioner reference for a vendor
  example of how data-handling and residency commitments are
  presented externally (see https://veriswarm.ai/trust); cited
  only as a practitioner example, not as an authoritative source.
```

## 3. Validation steps

To validate a completed policy before publishing it:

1. **Does every data class name a system?** "PII" is not a class;
   `users.email` is. Rows that only have labels, not systems, are
   not actionable.
2. **Does every tool in `AI_INVENTORY.md` have a column in §4?** If
   the team uses a tool that is not in the matrix, the policy is
   silent on it, and silence is dangerous.
3. **Is every §4 cell filled?** No blank cells; no "it depends"
   cells without the dependency named in the cell.
4. **Does any cell loosen the organisation policy?** If yes, the
   policy is wrong. Tighten the cell.
5. **Does every enforcement row have a named mechanism?** "The team
   is careful" is not a mechanism. Delete and replace with a
   specific hook, review item, or tabletop.
6. **Do the IP trailers show up in `git log`?** If the commit
   convention is in the policy but not in the history, the
   convention is not yet live. Mark it pending.
7. **Does legal see the §5–§6 sections before publication?** Legal
   is the owner of interpretation; the team owns operation. A
   policy that has not been structurally reviewed by legal is a
   draft.
8. **Can an IC make a decision in under 30 seconds using the
   matrix?** If the matrix requires reading the whole document,
   it has failed.
9. **Does the revision log exist?** `git log` against this file,
   with meaningful commit messages, *is* the revision log. If the
   log is empty or vague, the policy will drift.

## 4. Rubric

Grade a completed policy. 0–3 each. Pass bar: **18 / 27**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| D1 | **Data-class specificity** | Abstract labels only | Classes named | Classes named + systems named | Classes + systems + paths + owner per class |
| D2 | **Tool inventory alignment** | Tools not matched to `AI_INVENTORY.md` | Some alignment | All tools covered | All covered + admission process references this policy |
| D3 | **Matrix completeness** | Many blanks / "it depends" | Most cells filled | All cells filled | All filled + conditional cells name the condition |
| D4 | **Conservatism vs. corporate policy** | Loosens corporate | Matches corporate | Tightens where team-specific | Tightens with rationale; cross-references corporate |
| D5 | **IP decomposition** | One section labelled "IP" | Ownership covered | Ownership + third-party content + attribution covered distinctly | All three covered + explicit disclaimer that this is operational posture, not legal interpretation |
| D6 | **Confidentiality coverage** | Generic | Names customer contracts | Customer + partner NDAs | Customer + partner + embargoed internal + per-source overrides table |
| D7 | **Enforcement mechanisms** | Exhortation | Review items | Hooks + review items | Hooks + review items + audit log + status per row (live / partial / manual) |
| D8 | **Review process + triggers** | None | Quarterly | Quarterly + trigger list | Quarterly + trigger list + mandatory off-cycle enforcement |
| D9 | **Legal review + disclaimer** | No legal involvement | Legal reviewed informally | Legal reviewed structure; disclaimer present | Legal review documented in frontmatter; disclaimer separating operation from interpretation |

## 5. Common mistakes

- **"PII" as a data class.** "PII" is a legal label, not a system.
  The policy must name *where PII lives in the team's world* or
  the IC cannot classify at the moment of paste.
- **One tier per tool vendor.** "Consumer Claude" and "Enterprise
  Claude" are the same brand with different data boundaries. Tier
  by boundary, not by vendor name.
- **Letting the matrix have holes.** Blank cells invite
  interpretation; interpretation invites paste. Fill every cell.
- **Loosening the org policy.** The team policy is an
  operationalisation, not an alternative. Where the team cell is
  looser than the org rule, the cell is wrong.
- **Treating secrets as a data-class row rather than a
  never-everywhere row.** Secrets in any AI tool is prohibited,
  full stop. If the matrix implies otherwise for any cell, the
  policy has lost its own thread.
- **Conflating IP ownership with attribution.** They are separate
  topics. Ownership is "who holds the rights"; attribution is
  "what is labelled". The policy must address both.
- **No discussion of third-party content entering a prompt.**
  Copyleft content in a prompt, customer code pasted into an
  assistant, pre-release vendor docs quoted to a bot — all are IP
  risks the policy must address explicitly.
- **No mechanism to detect violations.** A policy that is
  violation-detectable only by self-report is a policy that will
  be violated. At least one row — typically secrets — must have an
  automated gate.
- **No "What this is not" section.** Without an explicit
  disclaimer, the policy reads as a legal interpretation and will
  be cited as one, and then legal will quite reasonably revise it.
- **Static policy.** Policies without a trigger-driven review
  cadence drift; drifted policies produce audit findings.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) —
  MAP 4.1 (characterise data origins), MEASURE 2 (ongoing
  measurement of data risks), MANAGE 4 (treatment of residual
  risk), and GOVERN 1.6 (inventory) are the structural anchors
  for §2–§4 of the policy. The policy is a team-scope per-tool
  per-class decomposition of those functions.
- [NIST AI 600-1: Generative AI Profile](https://nvlpubs.nist.gov/nistpubs/ai/NIST.AI.600-1.pdf) —
  the specific risk categories *data privacy*, *information
  integrity*, *information security*, and *intellectual property*
  that give §4 and §5 their vocabulary.
- **EU AI Act, Regulation (EU) 2024/1689** — cross-referenced for
  the AI-literacy half of the enforcement gate (via mod-110
  ex-02) and for the data-governance obligations applicable to
  deployers.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) —
  relevant to D3/D4 retrieval and agent memory rows (data
  poisoning, membership inference, model output as an
  exfiltration path).
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat
  taxonomy referenced in incident framing and in the retrieval
  security review items.
- [VeriSwarm Trust Center](https://veriswarm.ai/trust) —
  practitioner reference for a vendor example of how
  data-handling, residency, and non-training commitments are
  surfaced publicly; useful when the team is admitting a new
  vendor and wants to compare the vendor's trust surface to the
  team's expectations. Cited as a practitioner example only.
