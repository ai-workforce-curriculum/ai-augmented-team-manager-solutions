# Exercise 06 — Mapping OWASP LLM Top 10 to Your Review Checklist

> Module: `mod-103-review-standards-and-eval-commissioning`
> Exercise type: **design** (worked mapping + rationale + rubric)

## 1. Solution overview

The deliverable is a two-column mapping that connects each of the ten
categories in the **OWASP Top 10 for Large Language Model Applications** to
concrete review-checklist items and eval-suite coverage already defined in
this module (Exercises 01, 04, 05). The point of the mapping is to make it
*impossible to ship an LLM-touching change without explicitly thinking
about every OWASP category*, while not re-writing OWASP's descriptions in
a wiki no one reads.

A good mapping is:

- **One-to-many but not many-to-many.** Each OWASP category points at one
  or more specific checklist items. A checklist item may serve multiple
  categories (that's efficient coverage, not laziness) — but it must still
  be concretely checkable.
- **Scoped to your team's surface.** A SaaS team shipping prompts to
  internal users has different coverage priorities than a team embedding
  an LLM in a customer-facing agent. The mapping annotates each row with
  "applies here if…" rather than pretending universal fit.
- **Live.** OWASP updates the list; models and tooling shift. Mapping
  revision is a scheduled job, not a one-off exercise.

Design decisions this solution encodes:

1. **Cite OWASP's category IDs, not paraphrases.** LLM01, LLM02, … are the
   stable identifiers. Rename-drift is reduced when we point at IDs.
2. **Each row maps to (a) a checklist item, (b) an eval, (c) an
   organisational control.** Technical controls alone miss governance;
   checklist items alone miss automated gates.
3. **The team's risk tier drives *which* controls are mandatory.** Not
   every project needs a tool-sandbox and a data-governance review; the
   mapping says explicitly which categories gate at which tier.
4. **Open-ended rows are marked.** Where OWASP is still-ambiguous or the
   team hasn't decided (e.g. the exact `LLM10` interpretation for a given
   stack), the row is marked and sent back to the AI-practice lead.

> **A note on scope:** the module source policy lists the OWASP Machine
> Learning Security Top 10 (`owasp-ml-top-10`) as the official project.
> The OWASP Top 10 for Large Language Model Applications is a separate,
> also-official OWASP project. This exercise is specifically about the
> **LLM Top 10** per the exercise title, so the mapping below cites it
> directly. The ML Top 10 remains the canonical reference for classical
> ML-training-pipeline threats and is referenced in rows where a category
> overlaps.

## 2. Implementation — the mapping

### 2.1 Summary table

The IDs below correspond to the OWASP LLM Top 10 as published by the OWASP
GenAI Security project. Consult the project page for current definitions
before using this table in production decisions.

| OWASP ID | Category (summary) | Review-checklist item(s) | Eval coverage | Org control | Risk-tier gate |
|---|---|---|---|---|---|
| **LLM01** | Prompt Injection | Code checklist §Security: "untrusted input is not concatenated into system prompt or executed as tool call without allowlist/parser". Doc checklist §Safety: "could an agent ingest this and act on it?" | Direct-injection probe set in `tests/evals/<suite>/safety/injection.jsonl`; jailbreak assertions. | Prompt-template review by a second IC. Allowlist of tools callable from an agent. | Medium+ |
| **LLM02** | Insecure Output Handling | Code checklist §Security: "model output is not fed into `eval`, `exec`, SQL, shell, or HTML without sanitisation". | Output-fuzzing tests in eval suite; HTML/URL-scheme checks on rendered output. | Mandatory output-sanitisation wrapper at the call site (ADR'd, see Exercise 03 for the `no-llm-middleware` ADR — wrappers at call site, not global). | Medium+ |
| **LLM03** | Training Data Poisoning | N/A for most prompt-authoring teams; applies if the team fine-tunes or curates a RAG corpus. If so: provenance of every ingested doc. | Dataset provenance in commissioning rubric (Exercise 04, row C1); PII-and-source scanner on ingest. | Signed ingestion pipeline; quarantined "unsafe sources" set. | High if we fine-tune; otherwise N/A |
| **LLM04** | Model Denial of Service | Code checklist §Security (extension): "input length / batch-size is bounded; retries are capped". | Load eval: max-prompt-length, max-concurrent-requests, rate-limit response assertions. | Rate limiter + per-user budget; dashboard alert on cost spike. | Medium+ for any customer-facing endpoint |
| **LLM05** | Supply Chain Vulnerabilities | Code checklist §Security: "added deps pinned; no untrusted model weights pulled at runtime; no un-signed prompt bundles consumed". | Pin-check in the lint-policy package (Exercise 03, forbidden-imports mechanism, extended to model-weight URLs). | Dependency review (Dependabot / Renovate); SBOM for the service. | All tiers |
| **LLM06** | Sensitive Information Disclosure | Code checklist §Security: "no secret, key, token, or customer identifier in prompt template or logs". Doc checklist §Factual integrity: "no PII fields leaked to venues they don't belong in". | PII assertion in the eval suite (see Exercise 05 §2.7 `pii_check.py`); log-redaction tests. | Centralised logging with PII redaction; retention policy. | All tiers |
| **LLM07** | Insecure Plugin Design (a.k.a. **Insecure Tool Design** in later OWASP revisions) | Code checklist §Security: "every tool / function call the model can invoke has a parser and an allowlist of args; side-effecting tools require human confirmation when risk tier is High". | Tool-call test suite: unexpected args, missing fields, oversized payloads. | Tool registry: each tool lists blast radius, args schema, confirmation requirement. | Medium+ for any tool-calling agent |
| **LLM08** | Excessive Agency | Code checklist §Review-worthiness: "scope-creep item" (agent didn't expand the diff) + a new item for runtime: "agent cannot take an irreversible action without a human-in-the-loop checkpoint". | Trace-review eval: for a sample of agent runs, was the agent asked to do X and did it stop at X? | Named human approver for irreversible operations (deletes, charges, external messages). | All tiers; High tier makes it explicit at every call site |
| **LLM09** | Overreliance | Doc checklist §Factual integrity: "every claim cited to a source I personally opened". Code checklist §Author attestation: "I personally read every line of the diff". | Hallucination probes (does the model invent specs?) and factual-support judge (Exercise 05, layer 3). | Attribution UX: outputs show their sources where possible; disclosure of AI assistance in author-facing docs. | All tiers |
| **LLM10** | Model Theft | Mostly infra: don't expose the model weights, API keys, or gradients. Checklist: "keys not in code; model endpoints rate-limited and authenticated". | Rate-limit + auth eval probes. | Access control on fine-tuned model artifacts; audit log on weight downloads; key rotation cadence. | Applies if team hosts its own weights; otherwise reduces to LLM06+LLM05 |

### 2.2 How the mapping is used

- **Author side.** Before opening a PR that touches an LLM-adjacent file,
  the author scans the mapping table. For every row where their change
  could plausibly affect the category, they must be able to point at the
  checklist item they ticked or the eval that passed.
- **Reviewer side.** For High-risk-tier PRs, the reviewer reads the table
  as a mandatory review path: has LLM01 been thought about? LLM06? LLM08?
- **Manager side.** Quarterly, the team manager reviews the mapping for
  categories where coverage is "N/A" or "org control only" — these are
  the gaps most likely to bite in an audit, and they are the right items
  for next-quarter's eval investment.

### 2.3 Risk-tier gating (summarised)

| Tier | OWASP coverage required to merge |
|---|---|
| **Low** | LLM05, LLM06, LLM09 |
| **Medium** | Low + LLM01, LLM02, LLM04, LLM07, LLM08 |
| **High** | Medium + LLM03 (if we fine-tune or RAG), LLM10 (if self-hosted weights); plus a written sign-off from the AI-practice lead |

### 2.4 Decision rationale

- **Mapping to concrete checklist items rather than "comply with LLM0X".**
  Compliance-by-reference degenerates into a tick-box no one acts on. A
  pointer to a specific item ("§Security line 2 of the code checklist")
  survives an audit and shapes behaviour.
- **Separate columns for checklist, eval, and org control.** Each column
  covers a different failure mode. A checklist is fallible but cheap; an
  eval is deterministic but narrow; an org control is enforceable but
  slow. Three columns ensures no category is covered by hope alone.
- **Risk-tier gating.** Not every low-risk project needs every control.
  Saying so explicitly is governance in action — a universal mandate that
  the team cannot operationalise is worse than a tiered mandate it can.
- **"N/A for most prompt-authoring teams"** rows. LLM03 does not apply to
  a team that only writes prompts. Pretending it does trains engineers to
  ignore the mapping as busywork. Honesty about scope builds trust.
- **Open-question rows** are marked with a follow-up HTML comment so the
  auto-merge gate blocks until the AI-practice lead confirms.

## 3. Validation steps

1. **Trace every row back to OWASP.** For each row, open the OWASP LLM
   Top 10 page for that category. Confirm that the summary on your row
   matches the current project description; if OWASP has renamed or
   restructured the category, update the row.
2. **Trace every row forward to an artifact.** Each row's Checklist item
   must appear in Exercise 01's checklists verbatim or by clear
   reference. Each Eval column must map to a test file in the eval
   suite (even if the test is a stub). Each Org Control must map to an
   ADR, policy, or named runbook.
3. **Negative-test the gate.** Open a dummy PR that violates LLM01
   (concatenates untrusted input into a system prompt). It must fail the
   review checklist. Repeat for LLM02 (unsanitised output into `eval`)
   and LLM06 (logs a customer identifier).
4. **Tabletop a High-tier change.** Walk the mapping on a hypothetical
   High-tier change (e.g. a new tool the agent can invoke). The reviewer
   should land on at least LLM01, LLM02, LLM07, LLM08. If any of these
   rows are silent on the change, redline the mapping.
5. **Audit-dry-run.** Pretend an external auditor asks "how do you
   mitigate prompt injection?" The answer should be a 30-second walk:
   checklist item, eval suite path, allowlist ADR. If it's a 5-minute
   search, improve the mapping's linking.
6. **Quarterly rotation.** Set a recurring reminder (quarterly) to
   re-read OWASP's current list and reconcile. Categories evolve.

## 4. Rubric for grading a submitted mapping

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **All ten LLM categories present** | < 10 | 10 covered, some with only "TBD" | 10 covered, all with at least one artifact | 10 covered, each with checklist + eval + org control columns |
| R2 | **Each row points at concrete artifacts** | Prose claims only | Some links | Most rows link to specific files / sections | Every row links to specific files / sections |
| R3 | **Risk-tier gating explicit** | None | Single tier | 2 tiers | 3 tiers with per-category gating |
| R4 | **"N/A where applicable" honesty** | Pretends universal fit | Marks some as N/A unclearly | Marks N/A with scope condition | Marks N/A with scope condition + review-later flag |
| R5 | **Review cadence** | None | Mentioned | Scheduled | Scheduled + owner named |
| R6 | **Negative-test evidence** | None | "Tried it" | One scenario | Three scenarios spanning LLM01, LLM06, LLM08 |
| R7 | **References back to OWASP IDs** | Paraphrases only | Mixed | Mostly IDs | IDs with linked category pages |
| R8 | **Integration with Exercises 01/04/05** | Ignored | Mentioned | Referenced | Each row's checklist / eval column points at the corresponding Exercise artifact |

**Pass bar: 18/24.**

## 5. Common mistakes

- **Paraphrasing OWASP.** If your mapping drifts from the current OWASP
  description, auditors will ask which document you're actually citing.
  Use the IDs.
- **One flat column of "we comply".** Compliance claims are not controls.
  Each row needs an artifact.
- **Universal gating.** Requiring LLM03 mitigations on a team that
  doesn't fine-tune produces fatigue and compliance theatre. Scope
  honestly.
- **Treating the mapping as a one-time deliverable.** OWASP changes
  numbering and semantics across versions (and has reorganised under the
  GenAI Security Project umbrella). An unversioned mapping is a liability.
- **Collapsing checklist + eval + control into one column.** Each column
  covers a different failure mode; collapsing hides where coverage is
  missing.
- **Missing the "overreliance" row.** LLM09 is the one most team
  managers skip because it looks like a human-factors issue. It is the
  single most important category for a team-manager audience and the
  most difficult to automate.
- **Not naming a maintainer.** A mapping owned by "the team" is a
  mapping owned by nobody.
- **Using the mapping to replace, rather than organise, the checklist.**
  The checklist and the mapping are complementary — one is tactical, one
  is structural. Do not merge them.
- **Letting open-question rows sit.** The whole purpose of the mark
  is to block merge until resolved. If the marker isn't enforced, it is
  a wish, not a gate.

## 6. References

- [OWASP Top 10 for Large Language Model Applications](https://owasp.org/www-project-top-10-for-large-language-model-applications/) — the authoritative category list (LLM01–LLM10) this mapping is built on. The project now sits under the OWASP GenAI Security Project; consult the current page for the latest category semantics.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) — referenced where categories overlap (notably ML04 Membership Inference ↔ LLM06 Sensitive Information Disclosure, and ML06 AI Supply Chain ↔ LLM05 Supply Chain).
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat taxonomy used to sanity-check that this mapping's eval columns cover the techniques most likely to apply to LLM-touching changes (e.g. ATLAS tactics around model manipulation and information disclosure).
- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) — the Map, Measure, Manage, and Govern functions supply the structural reason for the three-column (checklist / eval / org control) design.
- Exercises 01, 03, 04, 05 (this module) — for the review checklists, ADR template, commissioning rubric, and Promptfoo / LangSmith walkthrough referenced in the mapping's cells.

<!-- follow-up: confirm the OWASP LLM Top 10 category labels and numbering against the version currently published by the OWASP GenAI Security Project before this mapping goes into production. LLM07 in particular has been renamed from "Insecure Plugin Design" toward "Insecure Tool Design" in later revisions; update the row title as needed and keep the IDs as the stable anchor. -->
