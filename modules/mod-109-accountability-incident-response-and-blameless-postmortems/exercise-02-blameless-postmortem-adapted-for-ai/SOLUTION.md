# Exercise 02 — Blameless Postmortem Adapted For AI

> Module: `mod-109-accountability-incident-response-and-blameless-postmortems`
> Exercise type: **design** (postmortem template + worked example + grading rubric)

## 1. Solution overview

The deliverable is a **blameless postmortem template that works on AI
incidents** — i.e., incidents where part of the system is a non-
deterministic model, and the "root cause" discipline inherited from
classical SRE fails in specific, predictable ways.

A blameless postmortem for an AI incident is:

- **Blameless at the people layer, uncompromising at the system layer.**
  No names in cause fields; every mechanism in the chain of failure is
  named and owned by a role, not a person.
- **Honest about non-determinism.** A deterministic SRE postmortem can
  say "the config was wrong; the config is now right." An AI-system
  postmortem often cannot. The template forces the author to state
  explicitly which portions of the failure are expected to be
  reproducible and which are not, and defers to exercise-05 for the
  regression-fixture mechanism that converts "probably won't happen
  again" into evidence.
- **Keyed to shared taxonomies.** OWASP ML Top 10, MITRE ATLAS, and
  NIST AI RMF labels appear in the header, next to the *mechanism*
  section, and in the follow-up action list. This is how a team's
  postmortems roll up into mod-110 governance reporting without
  translation.
- **Executable at the end.** The deliverable is not a document; it is
  a document plus a dated action list with named role-owners that lives
  in the team's tracker.

Design decisions this solution encodes:

1. **Blameless does not mean cause-less.** The failure mode where
   "blameless" becomes a euphemism for "we didn't find the cause" is
   worse than the original blame culture, because it forecloses
   learning. The template requires a *mechanism*; what it forbids is a
   *human-fault* attribution of that mechanism.
2. **A non-deterministic failure is still an incident.** "The model
   hallucinated once; we cannot reproduce it" is the most common
   dead-end in AI postmortems. The template forces the author to
   specify what the reproduction attempt was, and what the fallback is
   if reproduction fails (typically: ship a regression fixture, add a
   detection eval — see exercise-05).
3. **The postmortem names the human-in-the-loop decisions, not the
   humans.** When a reviewer approved a PR that shipped a bad prompt,
   the postmortem documents "the review process accepted this change
   without surfacing property X" — not "Alice approved it." The person
   is irrelevant to the mechanism; the review process is where the
   fix lives.
4. **Action items are owned by roles and dated.** An action item
   without an owner is a wish. An action item without a date is a
   permanent wish. The template rejects both.
5. **The "what went well" section is non-negotiable.** Teams that
   never write it lose the institutional memory of what their response
   process is good at, and over time strip away the parts that work in
   the name of "fixing things." The section is short; it is required.

## 2. Implementation — the postmortem template

### 2.1 Template document

```markdown
# Postmortem: <short, factual title — no blame, no speculation>

**Incident ID:** `INC-<yyyy-mm-dd>-<slug>`
**Severity:** SEV<n>
**Duration:** <declared → resolved>, total <HH:MM>
**IC:** <role, e.g. "copilot-ic on 2026-11-07">
**Author:** <role>
**Reviewers:** <role>, <role>, Risk Engineering, SecOps, Privacy (as applicable per exercise-04)
**Taxonomy labels:**
- OWASP ML Top 10: <e.g. ML09 Output Integrity Attack>
- MITRE ATLAS: <e.g. AML.T0051 LLM Prompt Injection>
- NIST AI RMF: <e.g. Measure 2.7 Safety>

## 1. Summary (two sentences, non-technical)

What happened, who was affected, what we did about it. This section is
quotable by Comms without rewriting. No cause claims that the Mechanism
section has not substantiated.

## 2. Impact

- **Users affected:** <count or scope>.
- **Data exposure:** <none | <category>>.
- **Financial / regulatory:** <none | <summary>>.
- **Reputational / trust surface:** <none | <summary>>.
- **Internal cost:** <engineer-hours in the response window>.

## 3. Timeline (UTC)

| Time | Event | Source |
|---|---|---|
| T-<hh:mm> | <pre-incident change that is in the mechanism chain> | <PR / deploy ID> |
| T0 | <first external or internal signal> | <monitor / ticket / channel> |
| +<hh:mm> | <declaration> | <PD> |
| +<hh:mm> | <containment action> | <runbook §3 step; command executed> |
| +<hh:mm> | <verifier confirmed> | <who, what check> |
| +<hh:mm> | <resolved> | <IC call> |

Every row has a source. No "the team noticed" without a source.

## 4. Mechanism (how the failure actually happened)

This section names the chain of steps that caused the incident at the
system level. It does **not** name people. It **does** name the
property of the review/eval/deploy process that failed to catch the
mechanism before it reached production.

Structure each mechanism as a short causal chain:

> `<precondition>` + `<trigger>` → `<model/system behaviour>` → `<external harm>`
>
> The property of our process that would have caught this earlier
> is `<property>`; that property was not present in the pipeline
> because `<reason>`.

For AI incidents, name explicitly:

- **Was the mechanism deterministic or probabilistic?**
  - *Deterministic* means: given the same prompt, tools, retrieval
    corpus, and model weights, the failure reproduces. These are the
    easy case; they look like classical SRE bugs.
  - *Probabilistic* means: the failure requires the model to roll the
    dice a specific way. These require a different follow-up: a
    detection eval and a regression fixture (see exercise-05), not a
    unit test.
- **Which inputs were in-scope for the pre-deploy evals?** Cite the eval
  harness (see mod-103). If the failing input was out-of-distribution
  for the eval set, name that explicitly.

## 5. Contributing factors

Not the root cause — things that made the mechanism more likely or more
damaging. Common categories for AI incidents:

- **Eval blind spots.** The pre-deploy eval did not include the input
  class that triggered the failure.
- **Permission surface.** The model had access to a tool or data source
  that it did not require for the use case.
- **Review-process gaps.** The PR review did not surface property X
  (specific, not "reviewers should have noticed").
- **Observability gaps.** The signal existed in logs but not in a
  monitor; or the monitor existed but did not route to the on-call.
- **Dependency / upstream.** Model provider shipped a new version; a
  system prompt change elsewhere interacted with a retrieval change
  here.

Each factor names what it is, not who caused it.

## 6. What we did well

Short. Required. Specific.

- "Containment was in effect within 9 minutes of declaration."
- "Snapshot-session.sh captured the triggering prompt before credentials
  were rotated, so we could reproduce offline."
- "Comms draft was approved and shipped before status page SLA lapsed."

If this section is empty, the author did not look hard enough. There
is always something that worked; if there is not, the incident would
have been larger.

## 7. What we did not do well

Specific. Named mechanisms of the response itself. No names.

- "The on-call did not have a one-command disable for tool Y; it took
  11 minutes to assemble the three-step mitigation. Runbook §8 known
  gap; now has an owner and a date."
- "The severity table in the runbook did not list retrieval
  cross-contamination explicitly; two engineers independently debated
  SEV1 vs SEV2 for ~6 minutes on the call."

## 8. Action items

Every action item has: an owner (role, not name), a date, a type, and
a linked ticket.

| ID | Action | Type | Owner | Target date | Ticket |
|---|---|---|---|---|---|
| A1 | <action> | **Prevent** \| **Detect** \| **Mitigate** \| **Recover** | <role> | YYYY-MM-DD | <link> |

For AI-specific incidents, at least one action item must fall under
**Detect**, because deterministic prevention of model misbehaviour is
not reliably achievable. Detection is what turns the first recurrence
into a 1-minute page instead of a 30-minute scramble.

For every incident with a probabilistic mechanism, there must be an
action item linking to the regression-fixture capture per exercise-05.

## 9. What we are explicitly not changing

Deliberate non-changes with reasoning.

- "We considered disabling tool Y globally. We are not, because <reason>.
  We are disabling tool Y for the affected tenant class only."
- "We considered rolling the model back to version N-1. We are not,
  because the safety-eval regression from N-1→N was not in this
  dimension; rollback would regress other safety dimensions."

This section is where the next on-call reads the shape of the
decision. Non-changes with no reasoning get re-litigated every
quarter.

## 10. Remaining unknowns

Facts we did not establish. Named, not hidden.

- "We cannot determine whether the prompt-injection payload was
  authored by the user or copy-pasted from elsewhere. We will not learn
  this; we are designing for the latter."
- "The probability of the probabilistic failure recurring is bounded
  only by the eval we are about to add; prior to that eval shipping,
  our estimate is 'low but unknown.'"

## 11. Taxonomy roll-up (for mod-110 reporting)

Short, structured; the governance layer consumes this.

- **OWASP ML Top 10:** <ID + label>
- **MITRE ATLAS tactic(s):** <AML.TAxxxx>, technique(s): <AML.T0xxx>
- **NIST AI RMF function/category:** <Govern|Map|Measure|Manage>, <subcategory>
- **Repeat?** <first-time | 2nd-time | ... — if 2nd+, link prior incident ID>

## 12. Appendix: evidence

- Snapshotted session(s): <hashes or paths>
- Retrieval log slice: <path>
- Model + prompt version pins at time-of-incident: <values>
- Safety-eval report for the deploy: <link>
- Regression fixture(s) captured: <link to exercise-05 artifact>

## 13. Ground rules reviewers should apply

- If a sentence names a person, the author rewrites it against a role.
- If a cause claim has no evidence row in §3 or §12, the author adds
  the evidence or weakens the claim.
- If a non-change in §9 has no reasoning, the author adds reasoning or
  removes the entry.
- If no action item has type **Detect**, the author adds one or
  justifies the absence in §10.
```

### 2.2 Worked example (fictional, used only to demonstrate structure)

The following is a **demonstration** of the template filled in against a
plausible but *fictional* incident. All names of systems and people
are placeholders. Teams adapting this must not treat the example as a
reference to a real incident. (Where data would come from a real-world
case, see exercise-03 for the AI-Incident-Database read-and-adapt
flow.)

```markdown
# Postmortem: Support Copilot leaked cross-tenant snippet in reply

**Incident ID:** `INC-2026-11-07-copilot-xtenant-leak`
**Severity:** SEV1
**Duration:** 2026-11-07 14:03Z → 2026-11-07 15:41Z (1h 38m)
**IC:** copilot-ic on-call
**Author:** copilot-team manager
**Reviewers:** copilot-team manager, Risk Engineering on-call, SecOps on-call, Privacy on-call
**Taxonomy labels:**
- OWASP ML Top 10: ML06 *AI Supply Chain Vulnerabilities* (retrieval
  pipeline contributed) and ML02 *Data Leakage* (user-facing symptom)
- MITRE ATLAS: AML.T0057 *LLM Data Leakage* (closest mapping)
- NIST AI RMF: Measure 2.10 *Privacy-Enhanced*; Govern 1.6 *Risk
  management processes*

## 1. Summary

The Support Copilot included a one-sentence excerpt from Tenant-B's
knowledge base in a reply to a user in Tenant-A. We disabled the
affected retrieval index for Tenant-A within 23 minutes of declaration
and purged the cached response.

## 2. Impact

- **Users affected:** 1 known user in Tenant-A; retrieval log suggests
  this is the only session where the cross-tenant chunk appeared.
- **Data exposure:** one sentence of Tenant-B's internal KB (non-PII,
  non-regulated).
- **Financial / regulatory:** reported to Privacy on-call; not a
  reportable breach under current contracts (confirmed by Legal).
- **Reputational / trust surface:** Tenant-A notified by Comms; no
  public disclosure required per Privacy guidance.
- **Internal cost:** ~14 engineer-hours response + review.

## 3. Timeline (UTC)

| Time | Event | Source |
|---|---|---|
| T-6h | Deploy `retrieval-index-v42` shipped; included a change to how
tenancy filters interact with the merged-index code path. | PR #4129, deploy log `dep-2026-11-07T08:03Z` |
| T0 (14:03) | Tenant-A customer-success contact reported the reply
contained "something that doesn't look like ours." | Zendesk ticket #SUP-88121 |
| +07m | On-call declared SEV1. | PagerDuty incident `pd-28c9` |
| +09m | Risk Engineering + SecOps paged per exercise-04 routing contract. | `#incident-copilot-28c9` |
| +11m | `./ops/snapshot-session.sh sess_7fc1b1` captured prompt + tool-call trace. | Runbook §3.3 |
| +23m | `./ops/disable-tool.sh retrieval-index-v42 --tenant tenant-a` confirmed. | Runbook §3.2; verifier Risk Eng on-call |
| +41m | Response cache purged for Tenant-A. | SecOps verifier |
| +1h 38m | Resolved; all three resolution criteria met. | IC call |

## 4. Mechanism

Precondition: `retrieval-index-v42` ships a change that moves the
tenancy-filter from the index-build step to the query step. Trigger:
a specific query embedding was close enough in cosine space to a
chunk in Tenant-B's partition that the merged-index reranker scored
it in the top-K *before* the per-query tenancy filter ran. Model
behaviour: the LLM dutifully cited the retrieved chunk, which was
Tenant-B's. External harm: Tenant-A's user saw a Tenant-B sentence.

This mechanism is **deterministic**: given the same embedding and the
same index state, it reproduces. We have reproduced it offline.

The property of our process that would have caught this earlier is
**per-tenant hermeticity testing of the retrieval path**. That property
was not present in the pipeline because the retrieval-index pre-deploy
test suite (mod-103 exercise-02) validates *within-tenant* top-K
quality, not *cross-tenant* leakage. No test attempted the
cross-tenant query shape before deploy.

## 5. Contributing factors

- **Eval blind spot:** retrieval-index evals score precision/recall
  within tenant; no cross-tenant leakage eval existed.
- **Review-process gap:** PR #4129's description described the
  code change honestly but did not call out that the tenancy-filter
  was moving between pipeline stages; the review did not surface it.
- **Observability gap:** we log retrieval provenance per-chunk in
  staging but not in production; this delayed diagnosis by ~20 min.

## 6. What we did well

- Containment completed within 23 minutes of declaration (SLA: 30).
- Snapshot captured the triggering session before any
  destructive action, so offline reproduction was available within
  the response window.
- Routing contract with Risk Eng + SecOps worked exactly as written;
  no paging delay, no scope dispute.

## 7. What we did not do well

- Three minutes were lost debating whether this was a retrieval bug
  or a model bug; the runbook trigger table did not list retrieval
  cross-contamination explicitly. Runbook update already merged.
- Production retrieval-provenance logging is behind staging; we knew
  this before the incident and had not prioritised closing the gap.

## 8. Action items

| ID | Action | Type | Owner | Target | Ticket |
|---|---|---|---|---|---|
| A1 | Add cross-tenant leakage eval to the retrieval-index pre-deploy harness | **Detect** | Copilot team manager | 2026-11-21 | PLAT-4211 |
| A2 | Capture and commit a regression fixture for this exact query shape (exercise-05) | **Detect** | Retrieval eng | 2026-11-14 | PLAT-4212 |
| A3 | Add retrieval-provenance logging to production | **Detect** | Retrieval eng | 2026-12-05 | PLAT-4213 |
| A4 | Update review template to require explicit call-out of security-relevant pipeline-stage moves | **Prevent** | Copilot team manager | 2026-11-19 | PLAT-4214 |
| A5 | Add retrieval cross-contamination row to runbook trigger table | **Prevent** | Copilot team manager | 2026-11-12 | DONE (merged) |

## 9. What we are explicitly not changing

- We considered rolling back to `retrieval-index-v41`. We are not,
  because v41 had its own correctness issues (PLAT-4087) and because
  the leakage window is now closed by the tenant-A disable and the
  cache purge. We will hotfix the tenancy-filter ordering in v42.
- We considered pausing the Support Copilot entirely. We are not,
  because the mechanism is scoped to the retrieval path and is
  already contained.

## 10. Remaining unknowns

- We cannot tell from logs whether this specific query shape existed
  earlier and silently retrieved cross-tenant content that the LLM
  then chose not to cite. We are designing A1's eval to detect
  retrieval-time leakage, not citation-time leakage.
- We have not fully explored whether other merged-index code paths
  share the same hazard. We will audit as part of A1.

## 11. Taxonomy roll-up

- **OWASP:** ML06 (supply-chain of the retrieval index), ML02 (data
  leakage symptom)
- **MITRE ATLAS:** AML.T0057 LLM Data Leakage
- **NIST AI RMF:** Measure 2.10 Privacy-Enhanced; Govern 1.6
- **Repeat?** First-time for this mechanism; first-time for
  cross-tenant retrieval leakage.

## 12. Appendix: evidence

- Snapshotted session: `s3://copilot-incident-evidence/INC-...-sess_7fc1b1.json.gz`
- Retrieval log slice: `s3://.../retrieval-log-2026-11-07T14.jsonl.gz`
- Model + prompt version pins at time-of-incident: `model=claude-3-5-sonnet-20241022`, `prompt=copilot-support@v213`
- Safety-eval report for the deploy: link to CI run
- Regression fixture: PLAT-4212 (pending)
```

### 2.3 Why the template looks like this

- **Taxonomy labels at the header.** By the time you reach §4 you need
  to know which vocabulary we are speaking. Labelling at the top also
  makes scanning by taxonomy possible — essential once mod-110
  governance wants to aggregate.
- **Mechanism and Contributing factors are separate sections.** The
  single most common blame-smuggling move in postmortems is burying
  "the reviewer should have caught it" inside the cause. Mechanism is
  the system; Contributing factors is where you say *what in the
  system allowed the mechanism to reach production*. Both are blameless;
  neither is cause-less.
- **Deterministic vs. probabilistic callout in §4.** This is the
  specifically-AI adaptation. Classical SRE postmortems do not need it.
  Omit it and the template produces documents that treat LLM output
  like a config file, which is false.
- **§9 non-changes.** Without this section, every decision looks like
  it was the only decision. With it, the shape of the tradeoff is
  preserved for the next on-call.
- **§10 unknowns.** Blameless does not mean omniscient. Publishing
  what you do not know is what distinguishes a postmortem from a
  justification.
- **§13 ground rules.** The template codifies the review discipline
  into the document itself, so a new reviewer does not have to bring
  their own.

## 3. Validation steps

To validate a completed postmortem before publishing:

1. **Name check.** Grep the document for every current and former team
   member's name. Any hit that is not in acknowledgements/author/IC
   lines is a blame leak; rewrite against a role.
2. **Cause-evidence check.** Every claim in §4 (Mechanism) must be
   traceable to a row in §3 (Timeline) or §12 (Evidence). If it is
   not, either add the evidence or weaken the claim to a hypothesis in
   §10 (Unknowns).
3. **Determinism check.** The deterministic-vs-probabilistic callout
   in §4 is present and specific. If the mechanism is probabilistic,
   §8 must contain an action item linking to the exercise-05
   regression-fixture capture.
4. **Detect check.** §8 contains at least one action item of type
   **Detect**. If it does not, §10 must explain why detection is not
   feasible for this mechanism.
5. **Role-ownership check.** Every action item has a role owner and a
   date. No "TBD" owners survive review.
6. **Non-change reasoning check.** Every row in §9 has a reason.
7. **What-went-well presence.** §6 is not empty.
8. **Taxonomy-label validity.** The labels in the header and §11 are
   from the published taxonomies (OWASP ML Top 10, MITRE ATLAS, NIST
   AI RMF), not invented locally. Check the ID against the source.
9. **Tenant / user privacy.** If the incident involved user data, the
   document does not include user identifiers beyond what Privacy
   approved. Impact in §2 is summary-level.

## 4. Rubric

Use this to grade a postmortem (self-assessment or peer review). Each
criterion scores 0–3. Pass bar: **20 / 30**.

| # | Criterion | 0 | 1 | 2 | 3 |
|---|---|---|---|---|---|
| R1 | **Blame-free language** | Names in cause | Mixed | Roles only | Roles only + explicit ground-rule statement in §13 |
| R2 | **Mechanism specificity** | Vague | Named at high level | Chain of steps | Chain of steps + property-that-would-have-caught-it + reason that property was absent |
| R3 | **Deterministic-vs-probabilistic callout** | Absent | Mentioned | Labelled | Labelled + implication for action items (detect/regression) wired through |
| R4 | **Taxonomy labels** | Absent | Framework name only | Specific IDs from one framework | Specific IDs from OWASP + ATLAS + NIST AI RMF where applicable |
| R5 | **Evidence trail** | Claims without sources | Some sources | Most claims sourced | Every §4 / §5 claim traces to §3 or §12 |
| R6 | **Action items** | No owner / no date | Owner or date | Owner + date + ticket | Owner (role) + date + ticket + type (Prevent/Detect/Mitigate/Recover) + at least one Detect |
| R7 | **"What we did well" present** | Absent | One line | Three specifics | Three specifics + explains why they worked |
| R8 | **Non-changes reasoned** | Absent | Listed | Listed + reasoned | Listed + reasoned + reader can re-decide next quarter |
| R9 | **Unknowns named** | Pretends omniscience | Names 1 unknown | Names unknowns + states whether they will be resolved | Names unknowns + states resolution plan + states design-for-the-unknown fallback |
| R10 | **Taxonomy roll-up for governance** | Absent | Labels repeated from header | Labels + repeat? flag | Labels + repeat? + prior-incident link if repeat |

## 5. Common mistakes

- **Blameless-as-euphemism.** Writing "the pipeline accepted the change"
  as a way of not saying anything is worse than writing "Alice merged
  the PR" — it preserves the blame in reader inference while removing
  the mechanism. The fix is to be *more specific* about the system, not
  less specific overall.
- **Cause-free "lessons learned."** A document whose §4 is three
  bullet points of adjectives ("process gaps," "insufficient testing")
  is not a postmortem. Specific mechanism or nothing.
- **Classical SRE five-whys on a probabilistic failure.** Asking "why"
  five times of a stochastic LLM output lands on "because the model
  rolled the dice that way." The five-whys discipline is not wrong,
  but it terminates on the training data and the eval harness, not
  on the model's internal state. The template reflects that.
- **No Detect action items.** Teams that only write Prevent actions
  rebuild the same fragility every quarter. Every AI-incident has at
  least one Detect action — a monitor, an eval, an alert.
- **Skipping §9.** Non-changes without reasoning are decisions without
  structure. They re-surface as proposals from the next new hire and
  the team spends an hour each time explaining why not.
- **Inventing taxonomy labels.** "We tagged it as an INJ-01" is not a
  taxonomy; it is a custom vocabulary. Use OWASP, ATLAS, NIST AI RMF
  IDs so the labels are portable.
- **Running the postmortem as a courtroom.** If the meeting opens with
  "who did what," the postmortem is already broken. The template
  front-loads mechanism and contributing factors specifically to
  remove the opportunity.
- **Shipping the document without an action owner review.** Action items
  without their intended owner reviewing them produce surprise and
  slippage. 15 minutes with owners before publish prevents it.

## 6. References

- [NIST AI Risk Management Framework (AI RMF 1.0)](https://www.nist.gov/itl/ai-risk-management-framework) —
  the *Govern* function's accountability expectations (risk-management
  processes, documentation, roles) and the *Manage* function's incident
  response categories are what the template's §8 (Action items), §9
  (Non-changes), and §11 (Taxonomy roll-up) exist to produce. The
  *Measure* function's subcategories (Safety, Privacy, Explainability,
  Fairness) populate the taxonomy roll-up row.
- [OWASP Machine Learning Security Top 10](https://owasp.org/www-project-machine-learning-security-top-10/) —
  provides the ML01–ML10 labels used in the header and taxonomy roll-up.
  The labels are deliberately pinned to specific IDs so they can be
  aggregated across teams.
- [MITRE ATLAS](https://atlas.mitre.org/) — adversarial-threat tactics
  and techniques. Incidents involving injection, data leakage, model
  theft, or plugin compromise map to specific AML.Txxxx IDs; the
  template requires the ID, not the family name, so that the governance
  layer (mod-110) can aggregate.

## 7. Open follow-up

The mapping between NIST AI RMF Measure subcategories and specific
action-item types (Prevent/Detect/Mitigate/Recover) could be
published as a per-team cheatsheet; currently the template relies
on the author's judgement. Candidate for exercise-05 follow-up.
